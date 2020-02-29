# Hung's blog

# Write math equations

Put the below code snippet in the respective post. There should be
another more elegant way, but I am good with this approach now.

``` html
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    processEscapes: true
  }
});
</script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

```

# Building on local environment

Install bundler. See https://bundler.io/

Install packages necessary to run the blog.
``` shell
bundle install
```

Serve the site locally
``` shell
bundle exec jekyll serve
```

# Converting ipython notebook to posts

``` shell
cd ~\_ipynb
python convert-hung.py ipython-notebook.ipynb
```

