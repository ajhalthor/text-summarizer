# text-summarizer
Summarizes Text. <br> 
Check out the live demo on <a href='https://www.youtube.com/watch?v=1PXGcUA3m18' target='_blank'>YouTube</a>. This algorithm we will discuss is my modification of the text summarizer developed by <a href="http://thetokenizer.com/2013/04/28/build-your-own-summary-tool/">Shlomi Babluki</a>.However, my version does offer more flexibility and improvements, which we’ll discuss later. Lets jump into the algorithm.

## The Algorithm
1. Take the full CONTENT and split it into PARAGRAPHS. 
2. Split each paragraph into SENTENCES. 
3. Compare every sentence with every other. This is done by Counting the number of common words and then Normalize this by dividing by average number of words per sentence

4. These intermediate scores/values are stored in an INTERSECTION matrix

5. Create the key-value dictionary
	- Key : Sentence
	- Value : Sum of intersection values with this sentence

6. From every paragraph, extract the sentences  with the highest score.

7. Sort the selected sentences in order of appearance in the original text to preserve content and meaning.

And like that, you have generated a summary of the original text.


## The Code
We are going to implement this using Java. So Lets take this one class at a time.

### 1. Sentence Class:
- The entire text is divided into a number of paragraphs and each paragraph is divided into a number of sentences.  We give 2 numbers to every sentence. The first is the `paragraphNumber` which indicates which paragraph this sentence is a part.

- `number` indicates the sentence number with respect to the entire text.

- `stringLength` is the number of characters in the Sentence. We really don't use this, but I’ll leave it here for now. 

- Every sentence will have a `score` that indicates it’s importance. This is initialized to 0.

- The number of words is computed by manually word tokenizing a sentence.

- A Sentence’s `value` is the actual string stored. 

### 2. Paragraph class

Every paragraph has a number associated with it and an Array List of sentences. A quick side note. An arrayList is a type of collection that we use to implement Sentences and Paragraphs. We could have also done this with simple arrays. But Collections better model at modeling the real world. Basically, it makes more sense.

### 3. SentenceComparitor Class

Comparitors, in general, are used to compare object. For example, we know that 5 is greater than 3 as they are both integers types. But How do we compare 2 Sentence objects with a number of properties? This is where a Comparitor comes in. To use a comparitor, we need to create a class that implements the comparator interface for the generic type that we wish to compare. Now, we override the compare function, passing in the 2 sentence Objects to be compared. In this case, One sentence is greater than the other if it’s score is greater.  

For example, if `obj1` has a score 3 and `obj2` has a score 6, then the Sentence Object `obj2` is __greater than__ Sentence Object `obj1`.

### 4. SentenceComparatorForSummary Class

This does the exact same thing as the SentenceComparitor Class. However, the greater object is not the one with the higher score, but the higher “number”. We will be using this in the Summary Tool Class while generating the Summary to ensure that the selected sentences are in the order they appear in the original text.

### 5. SummayTool Class

- This class takes care of all the operations from extracting sentences to generating the summary.

- The constructor and `init()` functions will instantiate and initialize all variables required.

- `extractSentenceFromContext()` method is a primitive hand built sentence tokenizer that extracts sentences based on the position of periods in the entire text. So, when a period is encountered, the text seen until that point is considered a sentence and added to the sentence list. Furthermore, paragraphs are also segmented. A new paragraph is encountered  if there are 2 consecutive linefeed characters present between 2 bodies of text.

- `groupSentencesIntoParagraphs()` method creates new Paragraph objects and stores the array list of Sentences present in them.

- `noOfCommonWords()` finds the number of common words between the 2 sentences. The comparison is not case sensitive.

- `createIntersectionMatrix()` creates a square matrix of size depending on the number of sentences in the entire context. Every sentence is compared with every other and an intermediate or partial score is generated using the normalization formula. This matrix will be symmetric about its diagonal, so we can reduce the time of execution by nearly half. The diagonal elements will be 1 because every sentence is 100% similar to itself.

- `createDictionary()` takes the partial scores computed in the intersection matrix and further computes the total score for every sentence. This is done by adding the all the scores of the row and assigning the value to the corresponding Sentence. So, this creates a dictionary where the key is the Sentence and the “value” is the Sentence Score.

- `createSummary()` will ultimately extract the final sentences for the summary. Some things, however, need to be taken care of. Certain paragraphs are longer than others. It wouldn't make sense to just select 1 sentence per paragraph. So, the number of sentences we select depends on the number of sentences in the paragraph. We select one sentence for every 5 sentences in a paragraph. Say an Article has 2 paragraphs with 6 sentences in the first and 17 in the second. From the 1st paragraph, we will select 2 sentences while from the second paragraph, we select 4.

- In the code, we iterate over every paragraph and determine the number of sentences to select from each. We then take the sentences and sort them in descending order of Score. So, the sentences of the paragraph will be arranged based on importance. We then select the required number of sentences from the beginning of the list to be a part of the final summary.

- Once all the important sentences have been selected, it is important to preserve the order in which they appeared. This is done by sorting the Sentences based on the Sentence “number” property which we implemented using the `SentenceComparatorForSummary` Comparitor.   

- `printSentences()` , printIntersectionMatrix, and printDicationary are developer methods. They display the corresponding structures. These are not required to generate the summary. But I will leave them in there so you can get an visualize of the intermediate steps for generating the summary.

- `printSummary()` prints the final  summary on the Console.

- `printStats()` prints the original word count, the summary word count and the amount of compression, which is the ratio of the 2.

### 6. improved_summary Class
And all of these methods are initiated from the main function in the main class.

## Run the Program

In the init() method of the Summary tool class, we defined the input file to be `samples/amazon/nexus_6p`. We are going to put all the class files into a `bin` folder. Execute te following command :

```
$ javac -d bin improved_summary.java
```

option d allows us to specify the path to store the generated class files. So all our class files are now in the bin folder. Now we execute the main class file by specifying the class path-the bin folder in this case.  

```
$java -classpath bin improved_summary
```
You will see the summary with stats displayed towads the bottom of the terminal window. See this code in action on <a href='https://www.youtube.com/watch?v=1PXGcUA3m18' target='_blank'>YouTube</a>


