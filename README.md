# Simple species mapping with R

## What is R?

R is a coding language with free distribution, designed to perform statistical analyses. R has a basic library of functions (addition / substraction), but an array of packages are available to perform a wide range of functions, including map making.

### Why using R

Because R is code-based, "scripts" (lines of of code to perform analyses) can easily be saved and rerun. For example, if you add data to a dataset, you can rerun your script and obtain an updated answer.

## Accesing R

### on HSU virtual labs:
Although you can download in your personal computer (and you are encouraged to do so – find instructions on Canvas), we will work on a standardized set up on the school virtual lab (vlabs). To access vlabs:
* [Click Here] (https://vlab.humboldt.edu)
* Access ‘vLab- Fall 2020’ or 'Desktops'
* Log in with your HSU information
* Go to the search icon to the bottom left and search : ‘Rstudio’
* Open R studio

### Setting up r in your computer
 
R is free, go to (https://www.r-project.org/) click on [download R] and select the link to [Oregon State University], which is the closest to California. Follow the steps and open R.

You can download Rstudio here (https://www.rstudio.com/)


### Some R resources
[Useful R Reference Cheatsheet by Tom Short](https://cran.r-project.org/doc/contrib/Short-refcard.pdf)
[Basic skills by Quick R](https://www.statmethods.net)
[Advanced skills Cheatsheet by Arianne Colton and Sean Chen](https://rstudio.com/wp-content/uploads/2016/02/advancedR.pdf)
[Other resources from YaRrr! The pirate’s guide to R](https://bookdown.org/ndphillips/YaRrr/r-resources.html)

## Tips to learn to code
* PRACTICE! A good practice consists on writing code. Do not copy and paste, do not assume that you got it by reading the tutorial: type it yourself.
* A better practice is to make sure you understand what each line of code is doing. Run line by line when you are following a tutorial
* Heavily annotate your code. Use # to annotate your code (see below to find out what that means).

## What does it look like and what does it all mean
![Fig1](files/Fig1_CIRMBioinf.jpeg) 
 
Figure 1 is a typical workspace in Rstudio, with four panels. You may have to open a new script (source) when you open the program for the first time: click on the new script icon to the top-left corner (see Fig 1, indicated with a yellow arrow). The most important panels right now are on the left, the source and the console. The source code in the top-left corner is where you will type your code and save your script. The console is where the code runs. You can write code directly to the console to find whether it works or not, or the answer to a calculation. You can also send your code from the source to the console to run. Importantly, you will be saving your source code, if you are typing in the console, you will be unable to save your progress or reuse your code. Therefore, I recommend you ALWAYS type and work on the source panel and send your code to run and see the output on the console. The panels on the right will display your data tables, plots, help and other features, but we will get to those later.

# Working with R

When you open R you will get a interactive window like this, also called the R console. 

You can write in this window and interact wih R directly. Let's created a variable "x" and asing the nuber "2" to it. Theype the floowing text and press enter.

```
x <- 2
```
Now when you type `x` in the console, it will return 2.
```
x
```

Because we want to create code that we can re-run, it is better to create a text document that we can execute in R. In order to do so, click on the white page icon at the top that when hover over says "Create, a new empty document on the editor" (in some cases new a new teext file has already been created automatically after opening Rstudio). In the new windown type:
```
x <- 2
y <- x + x
y
```

MAC: to run the code select it and press <kbd>command</kbd> + <kbd>return</kbd>
PC: to run the code selct it and press <kbd>control</kbd> + <kbd>r</kbd>
R is now making and addition with x, the returning value is then assigned to y. 

You can save the text file with the code by going to "file" in the menu and then "save". Save your script in any location you want

Congratulations, you have created your first script!

## Packages

Packages are group of functions that can be installed in your R environment. Dplyr is a great package for managing tables.

To install the package we neet to type:

```
install.packages("dplyr")
```

Now that the package is installed we need to load the package in R by typing

```
library(dplyr)
```

## Importing the dataset and creating a basemap

Let's install and load the packages nedeed for our exercise
```
install.packages(c("ggplot2","ggmap"))

library(ggplot2)
library(ggmap)
library(dplyr)
```

Today we are going to create a working directory. This will make importing and saving easier.
```
setwd("/Users/ov20/Desktop/Abronia")
getwd()
```

Now download the dataset and place it in such directory

https://www.dropbox.com/s/s9do9kz4zrur8pw/abronia_2.csv?dl=0

Once the dataset is in the "Abronia" directory you can import the data:

```
data <- read.csv("Abronia_2.csv")
head(data)
tail(data)
```

Like in our previous example we need to filter out data without a georreference
```
geodata <- data %>% filter(!is.na(decimalLatitude))
tail(geodata)
```

Now we can plot the records and do some basic filtering
```
plot(geodata$decimalLongitude, geodata$decimalLatitude)
geodata2 <- geodata %>% filter(decimalLatitude > 10)
plot(geodata2$decimalLongitude, geodata2$decimalLatitude)
```

Let's check how many species are in our dataset and how many records per species we have.
```
Ab_sp <- geodata %>% group_by(species) %>% tally()
```

Now we need to create a basemap. Notice that this map is different from the one we created with Darlingtonia
```
basemap <-  get_map(location = c(-140, 10, -70, 60), zoom = 4)
ggmap(basemap)
```

Now, let's map all the dataset points in the base map
```
ggmap(basemap) + geom_point(data = geodata2, aes(x=decimalLongitude, y=decimalLatitude, color=species))
```

## Creating a map for a single species
1. Create a subdataset for a single species
```
abla <- geodata2 %>% filter(species == "Abronia latifolia")
head(abla)
tail(abla)
```

2. Create a name for the resulting pdf file with the map
```
pdfName <- "Abronia latifolia.pdf" 
```

3. Draw the map
```
ggmap(basemap) + geom_point(data = abla, aes(x=decimalLongitude, y=decimalLatitude)) + ggtitle("Abronia latifolia")

```

4. Save the map
```
ggsave(filename = pdfName, plot=last_plot()) 
```


5. Create a name for the csv file
```
csv_name <- "Abronia_latifolia.csv"
```

6. Save the dataset just for one species, Abronia latifolia
```
write.csv(abla, csv_name)
```


Cool!

## Using a loop to draw a map for each species

In R a loop uses the floowing logic

for (item in item_list){
	perform_action_1
	perform_action_2
	etc
}

In our case our loop would look like this, notice that it contains every step used previously to create a single map
```
for (x in (Ab_sp$species)){
	print(x)
	# 1 creating a dataset for only one species
	sp_data <- geodata2 %>% filter(species == x)
	print("subset of ocurrences created")
	
	# 2 creating file name for map
	file_title <- paste(x, ".pdf", sep = "")

	# 3 creating the map
	ggmap(basemap) + geom_point(data = sp_data, aes(x=decimalLongitude, y=decimalLatitude), color ="blue") + ggtitle(x) # creating the map
	print("map created")

	# 4 saving plot in pdf						
	ggsave(filename = file_title, plot=last_plot()) 
	print(file_title)

	# 5 creating file name for dataset for every species
	dataset_title <- paste(x, ".csv", sep = "")

	# 6 saving dataset for species
	write.csv(sp_data, dataset_title)	
}

```

After runing the code you should be be able to see 23 pdf files in your working folder!
