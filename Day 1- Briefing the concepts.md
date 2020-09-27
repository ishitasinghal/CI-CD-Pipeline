---

---

# Day 1- Briefing the concepts

The first thing I started with today is to do a detailed study on the CI/CD pipeline. The main purpose to build a pipeline is to let our application be deployed to various clouds platforms such as AWS, Azure, and GCP. The information that I gathered from there has been summarized in the next few lines. 

Whenever a developer commits code to a version control system such as Github, the first part of our pipeline comes into 
action, that is the build phase and gets a version tag. 

After the build phase, the testing phase kicks off. Unit testing is done to test the small chunks of code. 

Finally, the code is further moved for deployment. The application is first deployed to the staging environment and only after the application has performed all the tests well in every environment, then only the application is deployed into the real production environment.

Feedbacks are always welcome and are taken care of as fast as possible. 

I tried to make a mini pipeline using basic tools. Reference has been taken from "https://medium.com/bettercode/how-to-build-a-modern-ci-cd-pipeline-5faa01891a5b"

1) PyCharm has been used for coding or generating a mini application, that is, just a random code snippet.

The code snippet is as follows:

```python
from __future__ import print_function
import random

buzz = ('continuous testing', 'continuous integration','continuous deployment', 'continuous improvement', 'devops')
adjectives = ('complete', 'modern', 'self-service', 'integrated', 'end-to-end')
adverbs = ('remarkably', 'enormously', 'substantially', 'significantly','seriously')
verbs = ('accelerates', 'improves', 'enhances', 'revamps', 'boosts')

def sample(l, n = 1):
    result = random.sample(l, n)
    if n == 1:
        return result[0]
    return result

def generate_buzz():
    buzz_terms = sample(buzz, 2)
    phrase = ' '.join([sample(adjectives), buzz_terms[0], sample(adverbs),
        sample(verbs), buzz_terms[1]])
    return phrase.title()

if __name__ == "__main__":
    print(generate_buzz())
```

2) Thereafter, some automated unit tests are included for which "pytest" is needed to be installed using the pip instruction from the command line and this requirement is listed in the requirements file. 

Automated test cases are written in another code snippet:

```python
import unittest

from buzz import generator

def test_sample_single_word():
    l = ('foo', 'bar', 'foobar')
    word = generator.sample(l)
    assert word in l

def test_sample_multiple_words():
    l = ('foo', 'bar', 'foobar')
    words = generator.sample(l, 2)
    assert len(words) == 2
    assert words[0] in l
    assert words[1] in l
    assert words[0] is not words[1]

def test_generate_buzz_of_at_least_five_words():
    phrase = generator.generate_buzz()
    assert len(phrase.split()) >= 5
```

3) The code is pushed on the VCS like Github for this case. 

#### Up till this, our coding, unit testing  and committing code to the VCS is done. Now our pipeline phase one begins.

4) Now we ensure automated testing whenever a code is pushed to the repository. 

Travis CI has been used for this purpose. It only requires a Github login, unlike Jenkins, which takes a lot of time in installation. It requires just a configuration file.

In Travis CI, a repository name is required for activation. A screenshot of the same has been added below:

[](https://github.com/ishitasinghal/internship2020/blob/master/images/repo active.png)

5) Next task is to add a ".travis.yml" file in the root of the project and finally push the code to the VCS again. The test outputs would be updated on the Travis CI.

[](https://github.com/ishitasinghal/internship2020/blob/master/images/travis.png)

6) The pipeline also brings the code analysis or code quality checks. For that purpose, the tool used is Better Code Hub.

[](https://github.com/ishitasinghal/internship2020/blob/master/images/best%20code%20hub.png)

7) Now, the code generator is converted into a website using flask in python. The code snippet is as follows:

```python
import os
from flask import Flask
from buzz import generator

app = Flask(__name__)

@app.route("/")
def generate_buzz():
    page = '<html><body><h1>'
    page += generator.generate_buzz()
    page += '</h1></body></html>'
    return page

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=int(os.getenv('PORT', 5000)))
```

For this, we need to add another library by the pip installer  and listed in the requirements file. 

[](https://github.com/ishitasinghal/internship2020/blob/master/images/web%20app.png)

