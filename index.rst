.. AutoKernel documentation master file, created by
   sphinx-quickstart on Wed Mar 11 12:03:10 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to AutoKernel Docs!
===================================


.. toctree::
  :maxdepth: 1
  :caption: Introduction
  :name: sec-introduction

  introduction/introduction.md


.. toctree::
  :maxdepth: 1
  :caption: Installation
  :name: sec-installation

  installation/install_from_source
  installation/install_from_docker

.. toctree::
  :maxdepth: 2
  :caption: Tutorials
  :name: sec-tutorial

  tutorials/autosearch
  tutorials/autokernel_plugin
  tutorials/tengine
  tutorials/halide/index



.. toctree::
  :maxdepth: 2
  :caption: Demos
  :name: sec-demo

  demo/gemm_optimization_x86



.. toctree::
  :maxdepth: 1
  :caption: Blog

  blog/autokernel_optimize_gemm_over_200_times_faster
  blog/ai_compiler overview
