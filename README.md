# ExtractJS: Extract JavaScript Code from Text Passages

The ExtractJS project addresses a specific problem encountered during the development of [Autocode](https://github.com/emoryhubbard/autocode): the precise detection and extraction of JavaScript code from text passages. The purpose of this capability is to help enable Autocode to accurately capture and run JavaScript code within an automated browser environment, avoiding errors and misinterpretations.

## Solution Overview

**[ExtractJS Demo Video](https://youtu.be/nbhQea9Y9FM)**

To address the challenge of accurately extracting JavaScript code from text, several approaches were attempted. Each of them have their pros and cons.

Initially, a neural network using Tensorflow was developed, by automatically generating training data (the make-training-data.ts API route), and the data manually labeled, as automatic labeling of the data, by eg. ChatGPT, proved too inaccurate. Due to the need for manual labelling, the initial size of the training dataset was small (around 1600 records, after balancing), and an accuracy of only 74% was achieved.

An alternative, potentially much more accurate approach using less data was researched using XGBoost. However, it would require manual feature engineering of each of the following features for highest efficiency, and it would preclude the ability to detect code lines on multi-line template literals:
- for loop header
- class declaration
- if header
- else header
- else if header
- return statement
- break statement
- continue statement
- while loop header
- do header
- do while footer
- switch header
- case header
- closing brace
- increment
- decrement
- function call
- let
- const
- var
- string concat beginning
- string concat
- string concat end
- numeric concat beginning
- numeric concat
- numeric concat end
- try header
- catch header
- finally header
- function declaration
- method declaration
- async function declaration
- async method declration
- export
- import
- await (both standalone and being used with assignment)
- property assignment (eg. a line inside JSON, maybe just call it JSON)
- array assignment (ie. a line inside array being assignment)
- comment (or maybe just has_comment?)
- has_comment
- word_count
- lowercase_start
- whitespace_start
- semicolon_end (also when there is a semicolon before code comment)
- template literal start
- template literal (anything here in between is technically impossible to detect properly)
- template literal end

These features would be engineered into a sci-kit learn pipeline so the resulting pipeline and its XGBClassifier model could be exported in an easy-to-deploy fashion for Autocode. However, before committing to that approach, alternative approaches and solutions were experimented with.

One of these was a workaround involving JavaScript parser libraries. Several of them exist and are capable of detecting whether a given string is valid JavaScript code, but aren't explicitly capable of detecting and extracting code snippets from text passages without developing a new algorithm. However, this ultimately became the preferred and most accurate solution, and was gradually tested and refined until no more errors have been detected.

## Development Environment

The development of ExtractJS involves the following tools and technologies:

- **Acorn**: Acorn, an open-source JavaScript parser, serves as the core component for parsing and interpreting text passages to extract JavaScript code, although it doesn't have the explicit capability to do so. Instead, an algorithm was created as a workaround that inteprets to and responds to error messages from the library in such a way that it can detect where functional code begins and ends, and extract it.
- **Node.js and Express**: Node.js and Express are used to build applications to help create training data for the neural network and gradient boosting approaches, as well as test the performance of the ExtractJS tool.
- **csv-parser**: csv-parser, a fast open-source CSV parser, is utilized for handling large CSV files and extracting data for training models and testing tools.
- **Jupyter Notebooks**: Jupyter Notebooks, developed in the Google Colab environment, are utilized for experimenting with neural networks using Tensorflow, as well as experimenting with gradient boosting using XGBoost.
- **TensorFlow and XGBoost**: TensorFlow and XGBoost are machine learning libraries that were explored as potential solutions for improving the accuracy of code extraction. While these technologies were experimented with, the JavaScript parser workaround utilizing Acorn emerged as the most accurate and preferred solution.

## Future Work

Moving forward, the ExtractJS project aims to:

- Further enhance the accuracy and efficiency of JavaScript code extraction.
- Explore additional techniques and methodologies for handling complex code extraction scenarios.
- Extend the project's capabilities to support a wider range of JavaScript code formats and variations.
