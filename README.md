# Comparing Standard Recurrent Neural Network and Long Short-Term Memory Neural Network on Char-RNN

**Author:** Mizuho Fukuda

**Course:** COGS 185 - Advanced Machine Learning Methods, UCSD

The GitHub repository for this study can be found [here](https://github.com/mf02511/Sherlock-Holmes-Generator).

You can view this paper [on this page](https://mf02511.github.io/Sherlock-Holmes-Generator/).

<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS_HTML">
  MathJax.Hub.Config({
    "HTML-CSS": {
      availableFonts: ["TeX"],
    },
    tex2jax: {
      inlineMath: [['$','$'],["\\(","\\)"]]},
      displayMath: [ ['$$','$$'], ['\[','\]'] ],
    TeX: {
      extensions: ["AMSmath.js", "AMSsymbols.js", "color.js"],
      equationNumbers: {
        autoNumber: "AMS"
      }
    },
    showProcessingMessages: false,
    messageStyle: "none",
    imageFont: null,
    "AssistiveMML": { disabled: true }
  });
</script>

## Abstract
This experiment intends to test the effectiveness of standard recurrent neural networks and long short-term memory networks through the Char-RNN model [2]. Using a complete set of Sherlock Holmes text dataset, two sets of Char-RNN models were trained using either a standard recurrent neural network or a long short-term memory model and the results were compared. The results were compared on the average loss of 3 trials over 20,000 iterations of training for each model. In addition, the performance of models were assessed based on the quality of generated text. The training loss for the two models indicated that the long short-term memory network significantly improves the model’s learning efficiency and the text generated by the long short-term memory network also indicated a more effective model.

## Introduction
Char-RNN is a very  simple yet practical character level language model presented by Karpathy in his blog “The Unreasonable Effectiveness of Recurrent Neural Networks” [2], which generates text by processing and learning from the input text on a character sequence level. Char-RNN is implemented using a recurrent neural network, a powerful artificial neural network that processes inputs and generates outputs in a sequential manner. One significant advantage of recurrent neural networks is its ability to “model data with temporal or sequential structure and varying length inputs and outputs” as opposed to standard neural networks that assumes each data point as an independent, randomly generated element [3]. While a standard neural network assumes a fixed length of input and output, recurrent neural networks deal with sequences of elements of varying lengths, each element dependent on the previous element in the sequence. Figure 1 below illustrates the structure of a recurrent neural network. The right side of the figure illustrates the unrolled network. At each step, the hidden state is computed using the input at that step as well as the previous hidden state.

![simple RNN](/assets/simple_rnn.png)

Figure 1: A standard recurrent neural network [5].

On the forward pass, the hidden state at each step is calculated using the following equation:

  $$h^{(t)} = \sigma(W^{hx}x^{(t)} + W^{hh}h^{(t-1)} + b_{h)}$$

The prediction $\hat y^{(t)}$ at time t is given by: 

  $$\hat y^{(t)} = softmax(W^{yh}h{(t)} + b_y)$$ 
  
where $W^{xh}$ denotes the weight vector and $W^{hh}$ denotes the weights for the hidden layer. Notice that the hidden state is computed using both the input $x^{(t)}$ and the previous hidden state $h^{(t-1)}$. This is what allows recurrent neural networks to make predictions based on sequential information.

When tuning the model provided in the Homework 3 Bonus Task 1, I noticed an issue with the performance [5]. No matter how long I train the model, the loss seems to only be decreasing by very little and becomes stagnant over time. The text generated by the resulting model is far from coherent. Furthermore, after 50,000 iterations of training, I noticed a peculiar trend in the training loss curve. Instead of converging, there were sudden spikes in the loss. This is due to what is known as the vanishing and exploding gradient problems in recurrent neural networks. The model outlined above is a standard recurrent neural network, which can be quite effective in learning short sequences but the training process becomes challenging with longer sequences due to the problems of vanishing and exploding gradients [3]. An exploding gradient can occur as large gradients are multiplied together during backpropagation, causing extremely large updates on the weights of the network, resulting in spikes such as ones mentioned above. A vanishing gradient is also troubling and can occur when backpropagating on a long sequence as the gradient becomes smaller and smaller to the point where the changes in weights in earlier layers become almost negligible, preventing the model from learning efficiently. The Long Short-Term Memory (LSTM) model attempts to alleviate this problem by introducing a memory cell in place of the hidden node. Each memory cell consists of an input gate, output gate, and a forget gate, which essentially controls what errors to let through. 

![two memory cells](/assets/two_memory.png)

Figure 2: A recurrent neural network with a hidden layer with two memory cells [3].

In short, Long Short-Term Memory models allow for learning long-term dependencies due to its ability to avoid vanishing gradients.

In this study, I compare two models of Char-RNN: one implemented with a standard recurrent neural network, the second implemented with a LSTM. The effectiveness of the models will be assessed based on the average loss over time as well as a subjective assessment of the performance of the generated text. 

## Method
The dataset used in this study is a complete set of Sherlock Holmes by the author Conan Doyle [1]. After removing non-printable strings from the text, the file contains a total of 3,381,831 characters. There are 100 possible characters including numbers, lowercase and uppercase alphabets, as well as several special characters. No data cleaning was necessary except for removing non-printable strings. The basic implementation of the model is taken directly from the skeleton code provided in Homework 3 Bonus Task 1 [5]. Two activation functions were compared against each other in this study: RNN and LSTM, using the *RNNCell()* and *LSTMCell()* classes from the PyTorch torch.nn package. Both models use a cross entropy loss function to calculate the training loss.

3 trials (separate networks) of 20,000 training iterations were performed on 2 different models (standard RNN and LSTM). Losses were recorded every 100 iterations and averaged over the 3 trials. The losses were then averaged over the 3 trials for each model and two training loss curves were plotted to assess whether there is a difference in their distribution. 

## Experiment

The null hypothesis is that there is no significant difference between the training loss curve for the RNN model and the LSTM model. Given the problem of vanishing gradient of RNN and the more sophisticated design of the LSTM, I predict a better overall performance by the LSTM model. It is expected that the LSTM model will have a faster rate of learning and will yield a better performing model after an equal number of training iterations. 

|   Iterations |  Standard RNN |          LSTM |
|:-------------|--------------:|--------------:|
|            1 |      3.054246 |      3.077060 |
|         5000 |      1.754787 |      1.610339 |
|        10000 |      1.694347 |      1.500792 |
|        15000 |      1.656148 |      1.442553 |
|        20000 |      1.674733 |      1.419844 |

Table 1: Average loss across 3 trials after 1 - 20,000 training iterations for RNN and LSTM models.

![loss curve](/assets/loss.png)

Figure 3: Average loss across 3 trials over 20,000 training iterations for RNN and LSTM models. Number of iterations shown in hundreds.

Table 1 and Figure 3 show the results of the experiment. It can be observed that the rate of decrease in the average loss of the RNN model is significantly slower compared to that of the LSTM model. The table also shows that the loss essentially plataus after 10,000 iterations for the RNN model at around 1.65  whereas the loss continues to decrease for the LSTM model, though at a lower rate. At the conclusion of the 20,000 training iterations, the RNN model has a loss of around 1.67 while the LSTM model has a loss of approximately 1.42. 

**Text generated by standard RNN model:**

>What---emolitial cocketing, suite the chara-erd hat beewly clayipay we, with. The stason tist would of, who ever-thalk rurgly, ky don't was the kide, the
>  us who up all our we, now. Acces, so durto ot,     sciped than I eregg was upon the mikh who indimemely mack to answery so werte. As the fury has
>  well orr contost. The, but the
>  where-has right after twat what had only compawionco for it, his, fow."
>
>  "I was is might to a farish conther for no, Mr. Holvelanills miscacNes, into to have tron comsirian mas was whoshionly scation," said the ratproturn to paperfold face. The nittl

**Text generated by LSTM model:**

>  Wan upon the Ader I come oy that you are may defires, you will most days and nom to the Trit must leght to this wards
>  traces at the few bringe Jack's moon of nothing with overeted to go sivination."
>
>  I had an trave, stoep over hard at the mile into endural slow at hard.
>
>  "We'll yarge hed. wancessped, platt fell been by the siqulinable taged: Matoultes of it to momence of an two jown that I were excelden messived warm attreating," to clemt entures and there, "with Browation, and a hable The'ld sint most gentle. "Interpling hall before perhops, for woman Squart, entirian cattife ou

Though the above generated texts are only a single sample from each model, one can clearly observe the difference in their quality. The text generated by the first model (RNN) contains many more spelling errors, non existent words, and misuse of punctuations compared to the one generated by the second model (LSTM), which seems to resemble real sentences, though not fully interpretable. 

Lastly, the better model (LSTM) was then trained for another 30,000 iterations (50,000 in total) and the generated text is as follows:

>Within
>  the
>  spoke
>  upon
>  all week.
>  "And he be a pennies.
>  "The matter and pattered by give used to answered,
>  and, might showed all to trut which had went them. I was night,
>  the points is not first I saw man, and
>  his shut clusch for he may never introg regasion. I wiger its. I saw anot, apperitually turned in the room, and a king the time, in those cry of that these last a matter to be can well 6zed these terrible or at the trust are away that I bake Lestrade, that his untalution own
>  unounse Stay handing Booker fact on the house is seven re obsers in his safe

Although still not fully comprehensible, the text is forming stories that resemble the original style of Sherlock Holmes and contains certain keywords that would be associated with a mystery novel. The formatting with empty space and paragraph breaks also resemble the format of a novel.

## Conclusion

The experiment effectively demonstrates the efficiency of the long short-term memory neural network over a standard recurrent neural network through the performance of the two distinct Char-RNN models. Over 20,000 training iterations, the limitations of a standard RNN becomes abundantly clear as the loss plateaus and learning stagnates, likely due to the issue of the vanishing gradient. One can truly appreciate the power of the architecture of the LSTM by comparing the text generated by the resulting models. 

With that said, however, there are several limitations to this study as well. Due to limited computational resources, only 3 trials were conducted for each model and they were only trained for 20,000 iterations for each trial. Though it is my belief that the number of iterations were sufficient in deriving a reasonable inference about the trend in the training loss curves, many more trials are needed to reach a statistically significant conclusion. In addition, the final Char-RNN model is far from ideal as better performance has been demonstrated in other studies [2] in which the model can generate coherent sentences. 

Regardless, the study further reinforces the fascinating effectiveness of the Char-RNN model that Karpathy [2] has illustrated in his blog. The ability for a recurrent neural network to process data in a sequential fashion certainly holds great potential for more interesting models.

## Reference
[1] Doyle, C. The complete Sherlock Holmes. 
   https://sherlock-holm.es/stories/plain-text/cnus.txt

[2] Karpathy, A. (2015). The Unreasonable Effectiveness of Recurrent Neural Networks. 
   Andrej Karpathy blog. http://karpathy.github.io/2015/05/21/rnn-effectiveness/

[3] Lipton, Z. C., Berkowitz, J., & Elkan, C. (2015, June 5). 
   A Critical Review of Recurrent Neural Networks for Sequence Learning. arXiv preprint arXiv:1506.00019.

[4] Tu, Z. (2023, May 11). Lecture 11: Recurrent Neural Networks [Lecture notes].
   COGS 185, Spring 2023 Advanced Machine Learning Methods, University of California, San Diego.

[5] Tu, Z. (2023). Homework Assignment 3.
   COGS 185, Spring 2023, Advanced Machine Learning Methods, University of California, San Diego.









