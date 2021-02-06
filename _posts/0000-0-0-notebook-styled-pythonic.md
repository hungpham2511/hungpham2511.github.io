---
title: "Notebook-styled python wrangling"
author: ["Hung Pham"]
date: 2020-06-21
categories: ["blog", "hugo"]
toc: "t"
draft: false
---

Using jupyter notebook is great when the task is relatively simple, or
when the main goal of the notebook is to analyze data **and** present
the data to others. These people might not have access to the dataset,
or the libraries that you are using. One can simply **package** a
Jupyter Notebook with all the graphs and analysis nicely rendered.

Writing jupyter notebooks, howerver, is hard work.  There are several
reasons. First, it's hard to write good code when the notebook reaches
a level of critical mass. It's always easier to copy the cells than
refactoring the code in a more reasonable manner.

Second, Writing code in a web browser is also quite inconvenient,
comparing to in emacs or another text editor.

In general, I just **feel** far less productive when using jupyter
notebook to write code, far away from the efficiency allowed by
emacs. And yes, I have tried all types of in-browser extensions that
jupyter has to offer, but none ever come close.

Some people have agreed: [blog post](https://www.sicara.ai/blog/2019-02-25-why-jupyter-not-my-ideal-notebook)


## Structure Python script {#structure-python-script}

Recently I have found that writing python scripts with structured
comments is much more enjoyable than in jupyter notebook. The [comment syntax](https://sphinx-gallery.github.io/stable/tutorials/plot%5Fparse.html#sphx-glr-tutorials-plot-parse-py)
is based on sphinx-gallery.

```python
################################################################################
# This is a block comment

x = 1
y = 2

################################################################################
# And this is another set of comments

z = x + y
print(z)
```

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


## Go full circle {#go-full-circle}

To generate jupyter notebook:

```sh
pip install sphinx-gallery  # if you have not already done that
sphx_glr_python_to_jupyter.py 2020-EtherCAT-1ms.py
```

Then to generate a fully filled notebook, use [this](https://nbconvert.readthedocs.io/en/latest/execute%5Fapi.html#executing-notebooks-from-the-command-line) command:

```sh
jupyter nbconvert --to notebook --execute mynotebook.ipynb
```

This notebook can then be converted to other formats or packaged and
sent directly to others.


## Related {#related}

[RunningJupyterNotebookViaSshOrGcloud]({{< relref "running-jupyter-notebook-via-ssh" >}})
[SshTunnelingPortForwarding]({{< relref "20200410105230-sshtunneling" >}})
