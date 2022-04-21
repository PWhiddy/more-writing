---
layout: post
mathjax: true
title: Language Models will become Vision Models (Draft)
--- 

Large language models (LLM) will become enormously more powerful when trained with text rendered into images rather than token embeddings. This follows the natural progression of models becoming more general, more multi-modal, and easier to interact with.

Once upon a time, ML models were laboriously trained for a single task. For example there were vision models which could classify images into a predetermined set of classes, and nlp models which could rate the sentiment of a sentence as positive or negative. Multitask models existed, but each task required it's own data adapter and loss function. Applying an ML model to any new task required significant expertise, resources and time. Transfer learning began to reduce these requirements, as models then could be effectively fine-tuned for new tasks using a relatively small amount of data and compute. However things really started to change with LLMs. Text-to-text Transfer Transformer (T5) demonstrated that any language task can be formulated simply as a "sequence to sequence" task, sharing the same data input representation and loss function between all. When GPT3 was released this paradigm was proven be a more than a small convenience. Now we can perform new tasks simply by "prompting" the model in natural language. Today a person with no programming experience can in 60 seconds create a system that exceeds the state-of-the-art methods from just 5 years ago. And even though this leap in generality and usability is a major breakthrough, huge opportunities still remain untapped.

What if a single model could understand and edit text, images, and any combination of the two? This actually could be much more straightforward to achieve than it seems. Models like CLIP already learn joint embeddings for text and images, but an approach more like that in "Masked Autoencoders Are Scalable Vision Learners" (MAE) could be much more flexible. 
Imagine any language or vision task formulated as just as Image-to-Image. Just as the user interface for language models collapsed into the model, so can the user interface for visual task.

For example, consider the following examples:

Text classification -> 

Review - "This piece of junk broke after one week!"
Question - "Is this review positive or negative {P} {N}

Text Summarization ->
What character is speaking at the bottom of the page?

Image classification -> 
This is an image of a ___

Image QA -> 
How many cats are in this image?
What website is this?
Has the flag been captured?
Is the vehicle destroyed? 

Image + text QA ->
What is this circled object?

Image Captioning -> 
Describe this scene

Image editing -> 
Where do I click to access this sites home page?
Color all of the cars red.
Circle all birds.

Image segmentation ->
cut out the cat from the background


FAQ:

    Q: Won't converting text-only tasks into images be wasteful of computation?

    A: Rendering text to an image plus the front of a vision model require only on the order of millions of flops. The forward pass of LLMs already require hundreds of billions of flops.

    Q: Will performance suffer because the model will simultaneously need to learn how to recognize and generate characters in addition to understanding their meaning?

    A: This will likely be true at first, but is less important than making capabilities broader. LLMs zero-shot performance is not as good as when they're fine-tuned for a specific task, but the zero-shot capabilities are much more useful because they're flexible and immediately ready to use. Additional data, parameters, and compute are probably ready to absorb this additional complexity anyway.

    Q: For many language tasks it's more difficult to measure correctness when the output is an image. For example, if the model generated a perfect answer, but the text isn't formatted exactly as ground truth knows it (for example it's shifted, or is the wrong color), standard pixel-wise loss won't be a great guide. ??
    
    A: This is probably the biggest challenge. Perhaps only masking small pieces of the image will help. Or maybe a second grader/discriminator/critic model which is able to judge the content of the output rather that is formatting could be trained jointly. 



One of von Neumann’s students at Princeton recalled that graduate students were being used to hand assemble programs into binary for their early machine. This student took time out to build an assembler, but when von Neumann found out about it he was very angry, saying that it was a waste of a valuable scientific computing instrument to use it to do clerical work.

John A.N. Lee, Dept of Computer Science, Virginia Polytechnical Institute