---
title: "Notebook-styled python wrangling"
author: ["Hung Pham"]
date: 2020-06-21
categories: ["python", "jupyter"]
toc: "t"
draft: false
---

Jupyter notebook is great in many scenarios.

A common use case is when my main objective is to analyze data _and_
present the analysis to others. I can simply save a notebook with all
the graphs and analysis nicely rendered and use it to report
results. In most cases, this is better than writing a Python script
because my colleagues usually do have access to the data-set, or the
required libraries to generate the analysis.

Writing Jupyter notebooks is hard work because of several
reasons. First, it's hard to write _good_ code, especially when a
notebook is slightly more complex. It's usually easier to copy code
cells and make minor modifications than to refactor duplicated
functionalities into modules. This usually happens when the notebook
becomes more somewhat more complex. Second, writing code in a web
browser is also quite inconvenient comparing to in Emacs or another
text editor.

In fact, I **am** far less productive and far more frustrated when
developing Jupyter notebook. And yes, I have tried all types of
in-browser extensions that jupyter has to offer, but none really
helps.

Apparently, I am not [alone](https://www.sicara.ai/blog/2019-02-25-why-jupyter-not-my-ideal-notebook).


## Structured Python scripts {#structured-python-scripts}

Python scripts with structured comments is an alternative to the
notebooks. Personally, it is much more enjoyable because I can use
Emacs instead of a browser to work. The structured [comments syntax](https://sphinx-gallery.github.io/stable/tutorials/plot%5Fparse.html#sphx-glr-tutorials-plot-parse-py) is
based on [`sphinx-gallery`](https://sphinx-gallery.github.io/stable/advanced.html).

A structured Python script can be separated into _cells_, similar to
Jupyter notebooks.

```python
"""
# An example notebook

Nothing is here.
"""

#%%
# # Introduction
# This is a simple introduction to structured python scripts.

################################################################################
# This is a block comment
x = 1
y = 2

################################################################################
# And this is another set of comments
z = x + y
print(z)

#%%
# # Plotting

import matplotlib.pyplot as plt
plt.plot([1,2,3], [3,2,3])
plt.show()
```

I can generate jupyter notebooks from a structured Python script using
`sphinx-gallery`. Save the above text to `script.py`.

```sh
pip install sphinx-gallery  # if you have not already done that
sphx_glr_python_to_jupyter.py script.py
```

This command produces a `script.ipynb` notebook that can be used
normally.  To execute the notebook, use [this](https://nbconvert.readthedocs.io/en/latest/execute%5Fapi.html#executing-notebooks-from-the-command-line) command:

```sh
jupyter nbconvert --to notebook --execute script.ipynb
```

This notebook can then be converted to other formats or packaged and
sent directly to others.


## Working with Emacs {#working-with-emacs}

Navigating this structured comments script is quite easy. I basically
list all lines what start with `# #` because they are usually header
of the comment blocks and often denote a section. And the occur buffer
looks like a table of content. See `python-occur-definitions` below.

```emacs-lisp
;; very useful function.
(defun occur-mode-clean-buffer ()
  "Removes all commentary from the *Occur* buffer, leaving the
   unadorned lines."
  (interactive)
  (if (get-buffer "*Occur*")
      (save-excursion
        (set-buffer (get-buffer "*Occur*"))
        (goto-char (point-min))
        (toggle-read-only 0)
        (if (looking-at "^[0-9]+ lines matching \"")
            (kill-line 1))
        (while (re-search-forward "^[ \t]*[0-9]+:" (point-max) t)
          (replace-match "")
          (forward-line 1))
        (goto-char (point-min))
        (while (re-search-forward "^# " (point-max) t)
          (replace-match "")
          (forward-line 1))
        (toggle-read-only 1))

    (message "There is no buffer named \"*Occur*\".")))

(defun python-occur-definitions ()
  "Display an occur buffer of all definitions in the current buffer.

  Also, switch to that buffer."
  (interactive)
  (let ((list-matching-lines-face nil))
    (occur "^ *\\(def\\|class\\|cdef\\|cpdef\\) \\|# #"))
  (occur-mode-clean-buffer)
  (let ((window (get-buffer-window "*Occur*")))
    (if window
        (select-window window)
      (switch-to-buffer "*Occur*"))))
```


## Related {#related}

[RunningJupyterNotebookViaSshOrGcloud]({{< relref "running-jupyter-notebook-via-ssh" >}})
[SshTunnelingPortForwarding]({{< relref "20200410105230-sshtunneling" >}})
