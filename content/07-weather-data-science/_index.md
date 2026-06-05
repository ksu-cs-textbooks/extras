+++
archetype = "chapter"
ordinal = "7"
title = "Weather Data Science"
pre = "<b>7. </b>"
weight = 7
+++

A STEM outreach activity using data science and machine learning techniques to analyze weather data. 

* Time: 60 - 120 minutes+
* Age: Middle School and Up

## Teacher Introduction Video

{{< youtube VYO093Aj-FA >}}

## Starter Code

The starter code can be found on [GitHub](https://github.com/alt-cs-lab/stem-weather). This repository is a template that can be easily forked by students or teachers to their own accounts. 

The files can also be downloaded as ZIP file from the [1.0 Release](https://github.com/alt-cs-lab/stem-weather/releases/tag/v1.0.0) and then redistributed through a course LMS or other process.

## Teacher Setup

For students to follow along with this activity, they need access to system with Python and [Jupyter Notebooks](https://jupyter.org/) installed and available. There are many possible configurations that work, which are discussed below. If you have questions or need assistance setting things up, reach out to altcs AT ksu DOT edu and we'll do our best to help!

### Simple - Local Installation

The simplest setup is to have [Python](https://www.python.org/downloads/) and [Visual Studio Code](https://code.visualstudio.com/) installed on the system locally. Then, within Visual Studio Code, install the [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) and [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter) extensions. If the folder containing the starter files are opened in Visual Studio Code, you should be prompted to install those extensions if they aren't already installed. 

### Devcontainers and GitHub Codespaces

The starter files also contain a `.devcontainer` folder that can be used to create a local [Development Container] using [Docker](https://www.docker.com/), or a cloud development environment using [GitHub Codespaces](https://github.com/features/codespaces). That environment will contain the Python installation and handle installing the required extensions and libraries. Students can create their own copies of the starter code using the [template repository on GitHub](https://github.com/alt-cs-lab/stem-weather). 

### Codio

The [Codio](https://www.codio.com/) platform supports [Jupyter Notebooks](https://docs.codio.com/common/develop/ide/editing/jupyter.html) and could be used to deliver this lesson. Teachers will need to configure the lesson in Codio for students to use. 

### Google Colab

[Google Colab](https://colab.research.google.com/) is another possible option for students to engage with this lesson online. Students can start by clicking on the link below to open the starter file in Google Colab (the same link can be found on [GitHub](https://github.com/alt-cs-lab/stem-weather)).

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/alt-cs-lab/stem-weather/blob/main/colab.ipynb)

It requires a bit of additional configuration:

* Run the code block at the top of the file to clone the repository and install the required libraries.
* Remember that all Python commands must be run within code blocks and not the terminal. See the example included in the starter notebook.

## Model Solution

The starter repository contains a `solution` folder that contains the intended model solution at the end of the activity. Since this activity is meant to be exploratory in nature, it can be helpful to give students an option to either write the code themselves or follow along with a model solution and make changes to the existing code. Teachers may choose to remove that folder if they redistribute these materials. 

## Lesson Plan

The lesson itself is broken into several parts. Teachers can build upon these parts by adding additional discussions, data sources, and activities. 

{{< children >}}
