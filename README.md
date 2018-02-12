# Fine-tuning a deep CNN with Caffe

Contents:

* [Get ready for this assignment](#get-ready-for-this-assignment)
* [How to complete the assignment](#how-to-complete-the-assignment)
* [Bonus questions](#bonus-questions)

## Get ready for this assignment

We suppose that you already followed instructions provided for the [tutorial](https://github.com/vvv-school/tutorial_dl-tuning#get-ready-for-the-afternoon), and are all set up with the VM, the `dl-lab` directory where to clone this repository, and the iCW dataset.

#### Get the code

Clone this repository:

```sh
$ cd $ROBOT_CODE/dl-lab
$ git clone https://www.github.com/vvv-school/assignment_dl-tuning.git
```

#### Compile the code

Compile the scripts that are provided with the repository (the same scripts as for the tutorial):

```sh
$ cd assignment_dl-tuning/scripts/src
$ mkdir build
$ cd build
$ ccmake ../
$ make
```

When compiling, check that things are set correctly like we suggested in the [tutorial](https://github.com/vvv-school/tutorial_dl-tuning#compile-the-code)

## How to complete the assignment

In this assignment you have to apply the fine-tuning protocol that we used in the tutorial, on a new task: object categorization between 4 different categories (sodabottle, mug, wallet and mouse). To this end, we will fine-tune our CNN on 8 object examples per category, validate the model on 1 object per category, and finally test the resulting model on the remaining 4 objects (1 per category, since we recall that in iCW we have 10 objects per category).

We already set up the script to generate the train/val/set splits, you will just need to run it (and understand how the splits are made).

Afterwards, you will have to check and configure the files for the definition of the network model and the optimization procedure.

After doing this, you will be able to train and test your model.

Follow step-by-step instructions below.

#### Generate the lists of train/val/test images

Open the `create_imagesets.py` script with a text editor, e.g.:

```sh
$ cd $ROBOT_CODE/dl-lab/assignment_dl-tuning
$ gedit create_imagesets.py
```

This script uses the configuration file `imageset_config.yml` (located inside `cat_4categories`) to select train/val/test images from the `iCW` dataset and create three corresponding files, containing the list of the selected images.

Look at lines 25-29 and check that the paths are correct (they should, if you are using the VM and followed instructions so far).

Run the script in the following way:

```sh
$ cd $ROBOT_CODE/dl-lab/assignment_dl-tuning
$ ./create_imagesets.py
```

Once done, check that a folder named `images_lists` inside `cat_4categories` has been created and contains the `train.txt`, `val.txt`, `test.txt` and `labels.txt`.

[**NOTE**] Look at the content of `imageset_config.yml`: can you understand how we are defining the train/val/test sets? Can you understand whether the generated image lists are correct, based on the configuration file?

#### Configure the definition of the network model

Open the `train_val.prototxt` and `deploy.prototxt` files, located in `cat_4categories/all-3`. Look for occurencies of the string `FILLME` (i.e. with `Ctrl-F`) and replace each occurence with the correct value.

[**HINT**] Although there are several parameters that should be checked (we saw them in the tutorial, and you can find them by looking for occurencies of the string `###`), luckily for you we ensure they are ok, and there is only one occurence for each file to be set!

#### Configure the definition of the optimization procedure

Similarly as before, open the `solver.prototxt` file located in `cat_4categories/all-3` and look for occurencies of the string `FILLME`, to be replaced with the correct value.

[**HINT**] In this case there are only 4 occurencies to be set: use the comments added in the file in order to understand how to do this.

#### Run the script for training and testing the model

Open the `train_and_test_net_.sh` script with a text editor, e.g.:

```sh
$ cd $ROBOT_CODE/dl-lab/assignment_dl-tuning/cat_4categories
$ gedit train_and_test_net.sh
```

All paths should be ok if you use the VM and followed our instructions, however, you can check that everything is pointed correctly on your system like we explained for the [tutorial](https://github.com/vvv-school/tutorial_dl-tuning#configure-and-understand-the-script).

Run the script:

```sh
$ cd $ROBOT_CODE/dl-lab/assignment_dl-tuning/cat_4categories
$ ./train_and_test_net.sh
```

[**NOTE**] If you get some error and want to run only some commands in the script (i.e. jump the training if you did it previously, and do only the test), comment with `#` the commands that you want to skip and run the script as above.

Look at the logging messages. The training should take around 20 minutes to complete and you should be able to see Caffe's output during the training, like in the [tutorial](https://github.com/vvv-school/tutorial_dl-tuning#run-the-script).

Monitor the learning process: how is the loss/accuracy going through iterations? Note, since we are using the `cross-entropy loss`, in the worst-case of random predictions (e.g. an output vector of size 4 and all elements set to 0.25) a reasonable value for the loss is around `-log(0.25)=1.4`, with accuracy around 0.25. Therefore, we expect the loss (accuracy) starting from these values, and then decreasing (increasing) at each iteration.

At the end of training, you should see image of the 4 tested objects, displayed one after the other: how is the model performing? What is the final accuracy reached on the test set? To answer to this question, remember to look at the files produced by the script:

```sh
$ cd $ROBOT_CODE/dl-lab/assignment_dl-tuning/cat_4categories
$ ls
```
The `lmdb_train` and `lmdb_val` folder contain the databases of the train and validation images, while the `mean.binaryproto` is the mean image of the training set.

Look in particular in the protocol folder:

```sh
$ cd $ROBOT_CODE/dl-lab/assignment_dl-tuning/cat_4categories/all-3
$ ls
```

1. check that the training produced the `final.caffemodel`!
2. have a look at the `caffeINFOtrain.txt` and `caffeINFOval.txt`, to have an idea of the train/validation performances achieved during training.
2. finally look at `test_acc.txt`: is it reasonable? How do you think could be improved?

## Bonus questions

1. Consider the tran/val/test splits that we adopted and modify them in order to train on less number of object examples per category (e.g. around 4), while maintaining a similar number of total training/validation images.

  Which is the accuracy achieved on the test set? Better or worse than in the first case? How would you explain this result?

  [**HINT**] To do this, you can clone the `cat_4categories` folder in a new one, where you will modify the `imageset_config.yml` file to generate the new image sets. You will then repeat the steps from [here](#generate-the-lists-of-train-val-test-images) on. In doing this, check the scripts that you call, and replace the name of the new folder in all places where the name of the original one appears.

2. Consider an object categorization task between all the six categories available in the `iCW` dataset, and fine-tune a network on this task.

  Which is the accuracy achieved on the test set? Better or worse than in the first case? How would you explain this result?

  [**HINT**] To do this, you can do as suggested for point 1.
