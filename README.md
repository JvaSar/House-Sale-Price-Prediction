# House-Sale-Price-Prediction
Home value predictions are used by realtors, mortgage lenders, and financial institutions to define loan types and rates. Predictions are also used by government agencies to overview the market trends, define policies in case that slower economic conditions or potential market bubbles that could affect negatively the housing market.
The purpose of the project is to predict home values based on the characteristics of the property. Using a multivariate regression, we will create a model that will predict the values.
For this project, we will be using a dataset obtained from an open data portal and reflects the home sales from the city of Ames, Iowa in the USA. The dataset has 81 variables.

```{R, load_libraries, message=F, warning=F}

install.packages("stringr")
install.packages("caret")
install.packages("boot")
install.packages("forecast")
install.packages("plotly")
install.packages("viridis")
install.packages("knitr")
install.packages("dygraphs")
install.packages("corrplot")
install.packages("RCurl")
install.packages("MASS")
install.packages("dplyr")
install.packages("ggcorrplot")
install.packages("VIF")
install.packages("car")
install.packages("gvlma")
install.packages("MASS")
install.packages("lattice")
install.packages("ggplot2")
library("dplyr")
library("plyr")
library("ggplot2")
library(downloader)

```
		
# Connceting Data
Sale Price and attributes of houses in the city of Ames, state of Iowa in United States of America

Source:https://www.kaggle.com/c/house-prices-advanced-regression-techniques/data

```{r}
setwd("C:/Users/j2valenc/Downloads")
test <- read.csv("test.csv",stringsAsFactors=FALSE)
train <- read.csv("train.csv",stringsAsFactors=FALSE)
attach(train)
attach(test)
# Merge data sets
data <- bind_rows(train, test)

```

```{r}
glimpse(data)
summary(data)
head(data)
print(dim(data))

```

# Dataset
- 2919 Records
- 81   Attributes

	data.frame':	2919 obs. of  81 variables:

		$ Id           : int  1 2 3 4 5 6 7 8 9 10 ...
		$ MSSubClass   : int  60 20 60 70 60 50 20 60 50 190 ...
		$ MSZoning     : Factor w/ 5 levels "C (all)","FV",..: 4 4 4 4 4 4 4 4 5 4 ...
		$ LotFrontage  : int  65 80 68 60 84 85 75 NA 51 50 ...
 		$ LotArea      : int  8450 9600 11250 9550 14260 14115 10084 10382 6120 7420 ...
 		$ Street       : Factor w/ 2 levels "Grvl","Pave": 2 2 2 2 2 2 2 2 2 2 ...
 		$ Alley        : Factor w/ 2 levels "Grvl","Pave": NA NA NA NA NA NA NA NA NA NA ...
 		$ LotShape     : Factor w/ 4 levels "IR1","IR2","IR3",..: 4 4 1 1 1 1 4 1 4 4 ...
 		$ LandContour  : Factor w/ 4 levels "Bnk","HLS","Low",..: 4 4 4 4 4 4 4 4 4 4 ...
 		$ Utilities    : chr  "AllPub" "AllPub" "AllPub" "AllPub" ...
 		$ LotConfig    : Factor w/ 5 levels "Corner","CulDSac",..: 5 3 5 1 3 5 5 1 5 1 ...
		$ LandSlope    : Factor w/ 3 levels "Gtl","Mod","Sev": 1 1 1 1 1 1 1 1 1 1 ...
		$ Neighborhood : Factor w/ 25 levels "Blmngtn","Blueste",..: 6 25 6 7 14 12 21 17 18 4 ...
		$ Condition1   : Factor w/ 9 levels "Artery","Feedr",..: 3 2 3 3 3 3 3 5 1 1 ...
		$ Condition2   : chr  "Norm" "Norm" "Norm" "Norm" ...
 		$ BldgType     : Factor w/ 5 levels "1Fam","2fmCon",..: 1 1 1 1 1 1 1 1 1 2 ...
 		$ HouseStyle   : chr  "2Story" "1Story" "2Story" "2Story" ...
 		$ OverallQual  : int  7 6 7 7 8 5 8 7 7 5 ...
 		$ OverallCond  : int  5 8 5 5 5 5 5 6 5 6 ...
 		$ YearBuilt    : int  2003 1976 2001 1915 2000 1993 2004 1973 1931 1939 ...
 		$ YearRemodAdd : int  2003 1976 2002 1970 2000 1995 2005 1973 1950 1950 ...
 		$ RoofStyle    : Factor w/ 6 levels "Flat","Gable",..: 2 2 2 2 2 2 2 2 2 2 ...
 		$ RoofMatl     : chr  "CompShg" "CompShg" "CompShg" "CompShg" ...
 		$ Exterior1st  : chr  "VinylSd" "MetalSd" "VinylSd" "Wd Sdng" ...
 		$ Exterior2nd  : chr  "VinylSd" "MetalSd" "VinylSd" "Wd Shng" ...
 		$ MasVnrType   : Factor w/ 4 levels "BrkCmn","BrkFace",..: 2 3 2 3 2 3 4 4 3 3 ...
 		$ MasVnrArea   : int  196 0 162 0 350 0 186 240 0 0 ...
 		$ ExterQual    : Factor w/ 4 levels "Ex","Fa","Gd",..: 3 4 3 4 3 4 3 4 4 4 ...
 		$ ExterCond    : Factor w/ 5 levels "Ex","Fa","Gd",..: 5 5 5 5 5 5 5 5 5 5 ...
 		$ Foundation   : Factor w/ 6 levels "BrkTil","CBlock",..: 3 2 3 1 3 6 3 2 1 1 ...
 		$ BsmtQual     : Factor w/ 4 levels "Ex","Fa","Gd",..: 3 3 3 4 3 3 1 3 4 4 ...
 		$ BsmtCond     : Factor w/ 4 levels "Fa","Gd","Po",..: 4 4 4 2 4 4 4 4 4 4 ...
 		$ BsmtExposure : Factor w/ 4 levels "Av","Gd","Mn",..: 4 2 3 4 1 4 1 3 4 4 ...
 		$ BsmtFinType1 : Factor w/ 6 levels "ALQ","BLQ","GLQ",..: 3 1 3 1 3 3 3 1 6 3 ...
 		$ BsmtFinSF1   : int  706 978 486 216 655 732 1369 859 0 851 ...
 		$ BsmtFinType2 : Factor w/ 6 levels "ALQ","BLQ","GLQ",..: 6 6 6 6 6 6 6 2 6 6 ...
 		$ BsmtFinSF2   : int  0 0 0 0 0 0 0 32 0 0 ...
 		$ BsmtUnfSF    : int  150 284 434 540 490 64 317 216 952 140 ...
 		$ TotalBsmtSF  : int  856 1262 920 756 1145 796 1686 1107 952 991 ...
 		$ Heating      : chr  "GasA" "GasA" "GasA" "GasA" ...
 		$ HeatingQC    : Factor w/ 5 levels "Ex","Fa","Gd",..: 1 1 1 3 1 1 1 1 3 1 ...
 		$ CentralAir   : Factor w/ 2 levels "N","Y": 2 2 2 2 2 2 2 2 2 2 ...
 		$ Electrical   : chr  "SBrkr" "SBrkr" "SBrkr" "SBrkr" ...
 		$ X1stFlrSF    : int  856 1262 920 961 1145 796 1694 1107 1022 1077 ...
 		$ X2ndFlrSF    : int  854 0 866 756 1053 566 0 983 752 0 ...
 		$ LowQualFinSF : int  0 0 0 0 0 0 0 0 0 0 ...
 		$ GrLivArea    : int  1710 1262 1786 1717 2198 1362 1694 2090 1774 1077 ...
 		$ BsmtFullBath : int  1 0 1 1 1 1 1 1 0 1 ...
 		$ BsmtHalfBath : int  0 1 0 0 0 0 0 0 0 0 ...
 		$ FullBath     : int  2 2 2 1 2 1 2 2 2 1 ...
 		$ HalfBath     : int  1 0 1 0 1 1 0 1 0 0 ...
 		$ BedroomAbvGr : int  3 3 3 3 4 1 3 3 2 2 ...
 		$ KitchenAbvGr : int  1 1 1 1 1 1 1 1 2 2 ...
 		$ KitchenQual  : Factor w/ 4 levels "Ex","Fa","Gd",..: 3 4 3 3 3 4 3 4 4 4 ...
 		$ TotRmsAbvGrd : int  8 6 6 7 9 5 7 7 8 5 ...
 		$ Functional   : Factor w/ 7 levels "Maj1","Maj2",..: 7 7 7 7 7 7 7 7 3 7 ...
 		$ Fireplaces   : int  0 1 1 1 1 0 1 2 2 2 ...
 		$ FireplaceQu  : Factor w/ 5 levels "Ex","Fa","Gd",..: NA 5 5 3 5 NA 3 5 5 5 ...
 		$ GarageType   : Factor w/ 6 levels "2Types","Attchd",..: 2 2 2 6 2 2 2 2 6 2 ...
 		$ GarageYrBlt  : int  2003 1976 2001 1998 2000 1993 2004 1973 1931 1939 ...
 		$ GarageFinish : Factor w/ 3 levels "Fin","RFn","Unf": 2 2 2 3 2 3 2 2 3 2 ...
 		$ GarageCars   : int  2 2 2 3 3 2 2 2 2 1 ...
 		$ GarageArea   : int  548 460 608 642 836 480 636 484 468 205 ...
 		$ GarageQual   : chr  "TA" "TA" "TA" "TA" ...
 		$ GarageCond   : Factor w/ 5 levels "Ex","Fa","Gd",..: 5 5 5 5 5 5 5 5 5 5 ...
 		$ PavedDrive   : Factor w/ 3 levels "N","P","Y": 3 3 3 3 3 3 3 3 3 3 ...
 		$ WoodDeckSF   : int  0 298 0 0 192 40 255 235 90 0 ...
 		$ OpenPorchSF  : int  61 0 42 35 84 30 57 204 0 4 ...
 		$ EnclosedPorch: int  0 0 0 272 0 0 0 228 205 0 ...
 		$ X3SsnPorch   : int  0 0 0 0 0 320 0 0 0 0 ...
 		$ ScreenPorch  : int  0 0 0 0 0 0 0 0 0 0 ...
 		$ PoolArea     : int  0 0 0 0 0 0 0 0 0 0 ...
 		$ PoolQC       : chr  NA NA NA NA ...
 		$ Fence        : Factor w/ 4 levels "GdPrv","GdWo",..: NA NA NA NA NA 3 NA NA NA NA ...
 		$ MiscFeature  : chr  NA NA NA NA ...
 		$ MiscVal      : int  0 0 0 0 0 700 0 350 0 0 ...
 		$ MoSold       : int  2 5 9 2 12 10 8 11 4 1 ...
 		$ YrSold       : int  2008 2007 2008 2006 2008 2009 2007 2009 2008 2008 ...
 		$ SaleType     : Factor w/ 9 levels "COD","Con","ConLD",..: 9 9 9 9 9 9 9 9 9 9 ...
 		$ SaleCondition: Factor w/ 6 levels "Abnorml","AdjLand",..: 5 5 5 1 5 5 5 5 1 5 ...
 		$ SalePrice    : int  208500 181500 223500 140000 250000 143000 307000 200000 129900 118000 ...

# The Dependent Variable
- SalePrice

## Description of the dependent variable
- Mean: 180,921;


 - Minimum: 34,900;
 - 1st Quartile:129,975;
 - Median: 163,000;
 - 3rd Quartile: 189,921;
 - Max: 755,000
 
 # Missing values in the dataset
 
 ```{r}
 sum(is.na(data))
 sapply(data, function(x) sum(is.na(x)))
 sapply(data,class)
 
 ```
 
 Graphic of missing values
 
 ```{r}
 x1 <- map_df (data, function(x){sum(is.na(x))})
 missing <- x1 %>% gather(key = "Variable") %>% filter(value > 0) %>% mutate(value = value/nrow(data))
 ggplot(missing, aes(x = reorder(Variable, -value),y = value)) + 
 geom_bar(stat = "identity", fill = "salmon") +   coord_flip()
 ```
 Missing Values all dataset
 
 ![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Percent%20Missing%20Values.png)
 
 Overview of the train dataset
 
 ```{r}
 glimpse(train)
 summary(train)
 head(train)
 print(dim(train))
 sum(is.na(train))
 sapply(train, function(x) sum(is.na(x)))
 
 ```

 Overview of the test dataset
 
 ```{r}
 glimpse(test)
 summary(test)
 head(test)
 print(dim(test))
 sum(is.na(test))
 sapply(test, function(x) sum(is.na(x)))
     
 ```

Removing attributes with high incidence of na- train dataset

Attributes that have high incidence (40% plus of missing values were removed)

```{r}
train$PoolQC<-NULL
train$MiscFeature<-NULL
train$Alley<-NULL
train$Fence<-NULL
train$FireplaceQu<-NULL
sapply(train, function(x) sum(is.na(x)))
> print(dim(train))
[1] 1460 records and   75 variables

```

Removing attributes with high incidence of na - test dataset

```{r}
test$PoolQC<-NULL
test$MiscFeature<-NULL
test$Alley<-NULL
test$Fence<-NULL
test$FireplaceQu<-NULL
sapply(test, function(x) sum(is.na(x)))
>print(dim(test))
[1] 1459  records and  74 variables

```
		 
 Removed attributes 
 
	-PoolQC has 2,909 missing values

	-MiscFeature has 2,814 missing values

	-Alley has 2,721 missing values

	-Fence has 2,348 missing values

	-FireplaceQu has 1,420 missing values

 
# Select Categorical Variables (factors)

Subset only factor variables from the train dataset

```{r}
fact_atrib<-train[,c(2,5:15,20:24,26:32,34,38:41,52,54,56,58,61:63,73,74)]
fact_atrib
names(fact_atrib)
is.factor(fact_atrib)
is.numeric(fact_atrib)
is.integer(fact_atrib)
summary(fact_atrib)

```

Summary of Categorical variables -train dataset	

```{r}
>summary(fact_atrib)

```
	MSZoning       Street      LotShape  LandContour  Utilities      LotConfig     LandSlope   Neighborhood   Condition1  
	C (all):  10   Grvl:   6   IR1:484   Bnk:  63     AllPub:1459    Corner : 263   Gtl:1382   NAmes  :225    Norm   :1260  
	FV     :  65   Pave:1454   IR2: 41   HLS:  50     NoSeWa:   1    CulDSac:  94   Mod:  65   CollgCr:150    Feedr  :  81  
	RH     :  16               IR3: 10   Low:  36                    FR2    :  47   Sev:  13   OldTown:113    Artery :  48  
	RL     :1151               Reg:925   Lvl:1311                    FR3    :   4              Edwards:100    RRAn   :  26  
	RM     : 218                                                     Inside : 1052              Somerst: 86   PosN   :  19  
                                                                                      	           Gilbert: 79    RRAe   :  11  
                                                                                      	           (Other):707    (Other):  15  
											 
	Condition2     BldgType      HouseStyle    RoofStyle       RoofMatl     Exterior1st   Exterior2nd    MasVnrType 
	Norm   :1445   1Fam  :1220   1Story :726   Flat   :  13   CompShg:1434   VinylSd:515   VinylSd:504   BrkCmn : 15  
	Feedr  :   6   2fmCon:  31   2Story :445   Gable  :1141   Tar&Grv:  11   HdBoard:222   MetalSd:214   BrkFace:445  
	Artery :   2   Duplex:  52   1.5Fin :154   Gambrel:  11   WdShngl:   6   MetalSd:220   HdBoard:207   None   :864  
	PosN   :   2   Twnhs :  43   SLvl   : 65   Hip    : 286   WdShake:   5   Wd Sdng:206   Wd Sdng:197   Stone  :128  
	RRNn   :   2   TwnhsE: 114   SFoyer : 37   Mansard:   7   ClyTile:   1   Plywood:108   Plywood:142   NA's   :  8  
	PosA   :   1                 1.5Unf : 14   Shed   :   2   Membran:   1   CemntBd: 61   CmentBd: 60                
	(Other):   2                 (Other): 19                  (Other):   2   (Other):128   (Other):136                

	ExterQual ExterCond Foundation   BsmtQual   BsmtCond    BsmtExposure BsmtFinType1 BsmtFinType2  Heating     HeatingQC
	Ex: 52    Ex:   3   BrkTil:146   Ex  :121   Fa  :  45   Av  :221     ALQ :220     ALQ :  19     Floor:   1   Ex:741   
	Fa: 14    Fa:  28   CBlock:634   Fa  : 35   Gd  :  65   Gd  :134     BLQ :148     BLQ :  33     GasA :1428   Fa: 49   
	Gd:488    Gd: 146   PConc :647   Gd  :618   Po  :   2   Mn  :114     GLQ :418     GLQ :  14     GasW :  18   Gd:241   
	TA:906    Po:   1   Slab  : 24   TA  :649   TA  :1311   No  :953     LwQ : 74     LwQ :  46     Grav :   7   Po:  1   
    	TA:1282             Stone :  6   NA's: 37   NA's:  37   NA's: 38     Rec :133     Rec :  54     OthW :   2   TA:428   
     		            Wood  :  3                                       Unf :430     Unf :1256     Wall :   4            
                                                                  	     NA's: 37     NA's:  38        
								     
	CentralAir Electrical   KitchenQual Functional    GarageType  GarageFinish GarageQual  GarageCond  PavedDrive
	N:  95     FuseA:  94   Ex:100      Maj1:  14   2Types :  6   Fin :352     Ex  :   3   Ex  :   2   N:  90    
	Y:1365     FuseF:  27   Fa: 39      Maj2:   5   Attchd :870   RFn :422     Fa  :  48   Fa  :  35   P:  30    
        	   FuseP:   3   Gd:586      Min1:  31   Basment: 19   Unf :605     Gd  :  14   Gd  :   9   Y:1340    
        	   Mix  :   1   TA:735      Min2:  34   BuiltIn: 88   NA's: 81     Po  :   3   Po  :   7             
        	   SBrkr:1334               Mod :  15   CarPort:  9                TA  :1311   TA  :1326             
        	   NA's :   1               Sev :   1   Detchd :387                NA's:  81   NA's:  81             
                                            Typ :1360   NA's   : 81  
				    
	SaleType    	SaleCondition 
	WD     :1267    Abnorml: 101  
	New    : 122    AdjLand:   4  
	COD    :  43    Alloca :  12  
	ConLD  :   9    Family :  20  
	ConLI  :   5    Normal :1198  
	ConLw  :   5    Partial: 125  
	(Other):   9                        

# Extracting  numeric variables - train set

```{r}
num_cols<-unlist(lapply(train, is.numeric))
num_cols
train_num<-train[,num_cols]
train_num
train_num<-na.omit(train_num)

```

## Summary of Numeric variables -train set

```{r}
> train_num<-na.omit(train_num)
> summary(train_num)

```
	MSSubClass      LotFrontage        LotArea        OverallQual      OverallCond      YearBuilt     YearRemodAdd 
	Min.   : 20.00   Min.   : 21.00   Min.   :  1300   Min.   : 2.000   Min.   :2.000   Min.   :1880   Min.   :1950  
	1st Qu.: 20.00   1st Qu.: 60.00   1st Qu.:  7590   1st Qu.: 5.000   1st Qu.:5.000   1st Qu.:1953   1st Qu.:1966  
	Median : 50.00   Median : 70.00   Median :  9416   Median : 6.000   Median :5.000   Median :1974   Median :1995  
	Mean   : 56.15   Mean   : 70.67   Mean   : 10123   Mean   : 6.212   Mean   :5.561   Mean   :1972   Mean   :1986  
	3rd Qu.: 70.00   3rd Qu.: 80.00   3rd Qu.: 11361   3rd Qu.: 7.000   3rd Qu.:6.000   3rd Qu.:2003   3rd Qu.:2005  
	Max.   :190.00   Max.   :313.00   Max.   :215245   Max.   :10.000   Max.   :9.000   Max.   :2010   Max.   :2010  

	MasVnrArea       BsmtFinSF1       BsmtFinSF2        BsmtUnfSF       TotalBsmtSF     X1stFlrSF      X2ndFlrSF     
	Min.   :   0.0   Min.   :   0.0   Min.   :   0.00   Min.   :   0.0   Min.   :   0   Min.   : 438   Min.   :   0.0  
	1st Qu.:   0.0   1st Qu.:   0.0   1st Qu.:   0.00   1st Qu.: 250.0   1st Qu.: 803   1st Qu.: 894   1st Qu.:   0.0  
	Median :   0.0   Median : 374.0   Median :   0.00   Median : 506.0   Median :1008   Median :1097   Median :   0.0  
	Mean   : 108.5   Mean   : 438.4   Mean   :  44.59   Mean   : 594.1   Mean   :1077   Mean   :1174   Mean   : 353.3  
	3rd Qu.: 170.0   3rd Qu.: 702.0   3rd Qu.:   0.00   3rd Qu.: 840.0   3rd Qu.:1324   3rd Qu.:1411   3rd Qu.: 728.0  
	Max.   :1600.0   Max.   :5644.0   Max.   :1474.00   Max.   :2336.0   Max.   :6110   Max.   :4692   Max.   :2065.0  

	LowQualFinSF       GrLivArea     BsmtFullBath     BsmtHalfBath        FullBath        HalfBath       BedroomAbvGr  
	Min.   :  0.000   Min.   : 438   Min.   :0.0000   Min.   :0.00000   Min.   :0.000   Min.   :0.0000   Min.   :0.000  
	1st Qu.:  0.000   1st Qu.:1155   1st Qu.:0.0000   1st Qu.:0.00000   1st Qu.:1.000   1st Qu.:0.0000   1st Qu.:2.000  
	Median :  0.000   Median :1479   Median :0.0000   Median :0.00000   Median :2.000   Median :0.0000   Median :3.000  
	Mean   :  4.568   Mean   :1531   Mean   :0.4139   Mean   :0.05531   Mean   :1.581   Mean   :0.3818   Mean   :2.864  
	3rd Qu.:  0.000   3rd Qu.:1776   3rd Qu.:1.0000   3rd Qu.:0.00000   3rd Qu.:2.000   3rd Qu.:1.0000   3rd Qu.:3.000  
	Max.   :572.000   Max.   :5642   Max.   :2.0000   Max.   :2.00000   Max.   :3.000   Max.   :2.0000   Max.   :6.000  

	KitchenAbvGr    TotRmsAbvGrd      Fireplaces      GarageYrBlt     GarageCars      GarageArea     WoodDeckSF    
	Min.   :1.000   Min.   : 3.000   Min.   :0.0000   Min.   :1900   Min.   :1.000   Min.   : 160   Min.   :  0.00  
	1st Qu.:1.000   1st Qu.: 5.000   1st Qu.:0.0000   1st Qu.:1959   1st Qu.:1.000   1st Qu.: 360   1st Qu.:  0.00  
	Median :1.000   Median : 6.000   Median :1.0000   Median :1981   Median :2.000   Median : 484   Median :  0.00  
	Mean   :1.043   Mean   : 6.576   Mean   :0.6039   Mean   :1978   Mean   :1.879   Mean   : 503   Mean   : 92.61  
	3rd Qu.:1.000   3rd Qu.: 7.000   3rd Qu.:1.0000   3rd Qu.:2003   3rd Qu.:2.000   3rd Qu.: 600   3rd Qu.:168.00  
	Max.   :3.000   Max.   :12.000   Max.   :3.0000   Max.   :2010   Max.   :4.000   Max.   :1418   Max.   :857.00  

	OpenPorchSF     EnclosedPorch      X3SsnPorch       ScreenPorch       PoolArea          MiscVal            MoSold     
	Min.   :  0.00   Min.   :  0.00   Min.   :  0.000   Min.   :  0.0   Min.   :  0.000   Min.   :   0.00   Min.   : 1.00  
	1st Qu.:  0.00   1st Qu.:  0.00   1st Qu.:  0.000   1st Qu.:  0.0   1st Qu.:  0.000   1st Qu.:   0.00   1st Qu.: 5.00  
	Median : 27.00   Median :  0.00   Median :  0.000   Median :  0.0   Median :  0.000   Median :   0.00   Median : 6.00  
	Mean   : 46.13   Mean   : 21.84   Mean   :  3.349   Mean   : 16.1   Mean   :  2.935   Mean   :  23.43   Mean   : 6.34  
	3rd Qu.: 68.00   3rd Qu.:  0.00   3rd Qu.:  0.000   3rd Qu.:  0.0   3rd Qu.:  0.000   3rd Qu.:   0.00   3rd Qu.: 8.00  
	Max.   :547.00   Max.   :552.00   Max.   :508.000   Max.   :480.0   Max.   :648.000   Max.   :2500.00   Max.   :12.00  

	YrSold       SalePrice     
	Min.   :2006   Min.   : 35311  
	1st Qu.:2007   1st Qu.:131000  
	Median :2008   Median :164900  
	Mean   :2008   Mean   :185506  
	3rd Qu.:2009   3rd Qu.:219500  
	Max.   :2010   Max.   :755000  

# Data imputation

## Numeric
The variables with missing values are:
LotFrontage, GarageYrBlt, MasVnrArea, GarageFinish,BsmtFullBath, BsmtHalfBath,TotRmsAbvGrd


## Imputation to Numeric variables

Using median values

```{r}
train$LotFrontage <-ifelse(is.na(train$LotFrontage),median(train$LotFrontage,na.rm=TRUE),train$LotFrontage)
train$GarageYrBlt <-ifelse(is.na(train$GarageYrBlt),median(train$GarageYrBlt,na.rm=TRUE),train$GarageYrBlt)
train$MasVnrArea <-ifelse(is.na(train$MasVnrArea),median(train$MasVnrArea,na.rm=TRUE),train$MasVnrArea)
train$GarageFinish <-ifelse(is.na(train$GarageFinish),median(train$GarageFinish,na.rm=TRUE),train$GarageFinish)
train$BsmtFullBath <-ifelse(is.na(train$BsmtFullBath),median(train$BsmtFullBath,na.rm=TRUE),train$BsmtFullBath)
train$BsmtHalfBath <-ifelse(is.na(train$BsmtHalfBath),median(train$BsmtHalfBath,na.rm=TRUE),train$BsmtHalfBath)
train$TotRmsAbvGrd<-ifelse(is.na(train$TotRmsAbvGrd),median(train$TotRmsAbvGrd,na.rm=TRUE),train$TotRmsAbvGrd)

```

## Categorical

Subset only factor variables from the train dataset

```{r}
fact_atrib<-train[,c(2,5:15,20:24,26:32,34,38:41,52,54,56,58,61:63,73,74)]

```
Categorical  variables with missing values:

GarageCond, GarageFinish, GarageQual, GarageType, BsmtExposure, BsmtFinType1,
BsmtFinType2, BsmtCond, BsmtQual, MasVnrType, Electrical,Utilities


## Visualize the percentage of missing values Categorical data

```{r}
install.packages("VIM")
library(VIM)
mice_plot <- aggr(fact_atrib,col=c('navyblue','yellow'),
                numbers=TRUE, sortVars=TRUE,
                labels=names(train), cex.axis=.7,
                gap=3, ylab=c("Missing data","Pattern"))
```
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Percent_MissingValuesCategorical.png)

## Imputation to Categorical Variables

         Predictive mean matching only imputes values that were actually observed for other units. The range of imputed values  always 		 lies between the minimum and the maximum of the observed real  values. Compared with standard methods based on linear       		regression and the normal distribution, PMM produces imputed values that are much more like real values. If the original 		variable is skewed,the imputed values will also be skewed. If the original variable is bounded by 0 and 100, the imputed values 	will also be bounded by 0 and 100. And if the real values are discrete (like number of children), the imputed values will also 		be discrete. That’s because the imputed values are real values that are “borrowed” from individuals with real data.
	source: https://statisticalhorizons.com/predictive-mean-matching

Using 'mice package' as the imputation method we used 'polyreg'=polytomous regression, which deals with cateogorical attributes

```{r}
install.packages("mice")
library(mice)
install.packages("MASS")
install.packages("nnet")
install.packages("nlme")

imp<- mice(fact_atrib, m=5 ,maxit=10, method ='polyreg')

```

verify that missing values were imputed

```{r}
imp
summary(imp)

## create a dataset after imputation
imputed<-complete(imp)

```
	
Check for missings in the imputed dataset

```{r}
sapply(imputed, function(x) sum(is.na(x)))
summary(imputed)
print(dim(imputed))

fact_atrib<- (imputed)
summary (fact_atrib)

#check imputing method
imp$method
	
```
 
# Correlation for Categorical Variables


```{r}
library(corrplot)
install.packages ("GGally")
library(GGally)

ggcorr(train, 
   	label = TRUE, 
   	label_alpha = TRUE)

```

## Plot of Variables with high correlation 

```{r}
qplot(train$GarageYrBlt, 
  	train$YearBuilt,
  	data = train, 
  	geom = c("point","smooth"),
  	method = "lm",
  	alpha = I(1/5), 
  	se = FALSE
```
	
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/GarageYrBuitl_YearBuilt.png)

Distribution levels for categorical variables

verify levels of each factor

```{r}
levels (fact_atrib$Neighborhood)
table(fact_atrib$Neighborhood)

```

## Plot Categorical Variables

```{r}
barplot(table(fact_atrib$MSZoning))
barplot(table(fact_atrib$Street))
barplot(table(fact_atrib$LotShape))
barplot(table(fact_atrib$LandContour))
barplot(table(fact_atrib$Utilities))
barplot(table(fact_atrib$LotConfig))
barplot(table(fact_atrib$LandSlope))
barplot(table(fact_atrib$Neighborhood),ylab="Frequency",col="green",border="blue")
barplot(table(fact_atrib$Condition1))
barplot(table(fact_atrib$Condition2))
barplot(table(fact_atrib$BldgType))
barplot(table(fact_atrib$HouseStyle))
barplot(table(fact_atrib$RoofStyle))
barplot(table(fact_atrib$RoofMatl))
barplot(table(fact_atrib$Exterior1st))
barplot(table(fact_atrib$Exterior2nd))
barplot(table(fact_atrib$MasVnrType))
barplot(table(fact_atrib$ExterQual))
barplot(table(fact_atrib$ExterCond))
barplot(table(fact_atrib$Foundation))
barplot(table(fact_atrib$BsmtQual))
barplot(table(fact_atrib$BsmtCond))
barplot(table(fact_atrib$BsmtExposure))
barplot(table(fact_atrib$BsmtFinType1))
barplot(table(fact_atrib$BsmtFinType2))
barplot(table(fact_atrib$Heating))
barplot(table(fact_atrib$HeatingQC))
barplot(table(fact_atrib$CentralAir))
barplot(table(fact_atrib$Electrical))
barplot(table(fact_atrib$KitchenQual))
barplot(table(fact_atrib$Functional))
barplot(table(fact_atrib$GarageType))
barplot(table(fact_atrib$GarageFinish))
barplot(table(fact_atrib$GarageQual))
barplot(table(fact_atrib$GarageCond))
barplot(table(fact_atrib$PavedDrive))
barplot(table(fact_atrib$SaleType))
barplot(table(fact_atrib$SaleCondition))
plot(x = fact_atrib$SaleCondition,y = train$SalePrice)
plot(x =fact_atrib$MSZoning,y = train$SalePrice)
plot(x =fact_atrib$Street,y = train$SalePrice)
plot(x =fact_atrib$LotShape,y = train$SalePrice)
plot(x =fact_atrib$LandStreet,y = train$SalePrice)
plot(x =fact_atrib$Street,y = train$SalePrice)
plot(x =fact_atrib$LotShape,y = train$SalePrice)
plot(x =fact_atrib$LandContour,y = train$SalePrice)
plot(x =fact_atrib$Utilities,y = train$SalePrice)
plot(x =fact_atrib$LotConfig,y = train$SalePrice)
plot(x =fact_atrib$LandSlope,y = train$SalePrice)
plot(x =fact_atrib$Condition1,y = train$SalePrice)
plot(x =fact_atrib$Condition2,y = train$SalePrice)
plot(x =fact_atrib$BldgType,y = train$SalePrice)
plot(x =fact_atrib$HouseStyle,y = train$SalePrice)
plot(x =fact_atrib$RoofStyle,y = train$SalePrice)
plot(x =fact_atrib$RoofMatl,y = train$SalePrice)
plot(x =fact_atrib$Exterior1st,y = train$SalePrice)
plot(x =fact_atrib$Exterior2nd,y = train$SalePrice)
plot(x =fact_atrib$MasVnrType,y = train$SalePrice)
plot(x =fact_atrib$ExterQual,y = train$SalePrice)
plot(x =fact_atrib$ExterCond,y = train$SalePrice)
plot(x =fact_atrib$Foundation,y = train$SalePrice)
plot(x =fact_atrib$BsmtQual,y = train$SalePrice)
plot(x =fact_atrib$BsmtCond,y = train$SalePrice)
plot(x =fact_atrib$BsmtExposure,y = train$SalePrice)
plot(x =fact_atrib$BsmtFinType1,y = train$SalePrice)
plot(x =fact_atrib$BsmtFinType2,y = train$SalePrice)
plot(x =fact_atrib$Heating,y = train$SalePrice)
plot(x =fact_atrib$HeatingQC,y = train$SalePrice)
plot(x =fact_atrib$CentralAir,y = train$SalePrice)
plot(x =fact_atrib$Electrical,y = train$SalePrice)
plot(x =fact_atrib$KitchenQual,y = train$SalePrice)
plot(x =fact_atrib$Functional,y = train$SalePrice)
plot(x =fact_atrib$GarageType,y = train$SalePrice)
plot(x =fact_atrib$GarageFinish,y = train$SalePrice)
plot(x =fact_atrib$GarageQual,y = train$SalePrice)
plot(x =fact_atrib$GarageCond,y = train$SalePrice)
plot(x =fact_atrib$PavedDrive,y = train$SalePrice)
plot(x =fact_atrib$SaleType,y = train$SalePrice)
plot(x =fact_atrib$SaleCondition,y = train$SalePrice)

```

# Correlation for Numeric Variables 

```{r}
install.packages("corr")
install.packages("corrplot")
install.packages ("corrgram")
require(corrplot)
require(corr)
library(corrplot)
library(corrgram)

## Pair-wise correlations using pearson spearman coefficients
load the libraries
library(mlbench)
load the dataset
data(M)

```

Correlation matrix for numeric variables

```{r}
correlations <-(round(cor(M[,1:8], method="perason")),2)

# display the correlation matrix

print(correlations)

```

## Plot correlations

```{r}
M = dataframe of numerical variables
train_num<-na.omit(train_num)
M<-cor(train_num)
corrplot(M, method="number")
corrplot(M, method="color")
corrplot(M, method="color",tl.cex=0.7)

```

## Correlation matrix - Numeric variables

A correlation matrix represents the pair correlation of all the variables.

a correlation cannot be computed for factor variable. We need to make sure we drop categorical feature before we pass the

data frame inside cor()

```{r}
> Correlation_Numeric <-(M)
> round(cor(M),2)
> print(correlation_Numeric)

> correlations <- cor(M[,1:8])
> # display the correlation matrix
> print(correlations)

```

         	        MSSubClass LotFrontage LotArea   OverallQual  OverallCond YearBuilt   YearRemodAdd MasVnrArea
	MSSubClass    1.00000000  -0.6723859 -0.51385068  -0.1453619 -0.06886341 -0.07492835  -0.09199908 -0.0971567
	LotFrontage  -0.67238594   1.0000000  0.73264888   0.4560600 -0.26767225  0.26422047   0.21277702  0.4406207
	LotArea      -0.51385068   0.7326489  1.00000000   0.3116059 -0.17676899  0.13655180   0.08095627  0.3001137
	OverallQual  -0.14536187   0.4560600  0.31160594   1.0000000 -0.58807156  0.85620630   0.84827002  0.7825370
	OverallCond  -0.06886341  -0.2676722 -0.17676899  -0.5880716  1.00000000 -0.71014143  -0.42985549 -0.5560941
	YearBuilt    -0.07492835   0.2642205  0.13655180   0.8562063 -0.71014143  1.00000000   0.89924601  0.6731973
	YearRemodAdd -0.09199908   0.2127770  0.08095627   0.8482700 -0.42985549  0.89924601   1.00000000  0.5610407
	MasVnrArea   -0.09715670   0.4406207  0.30011374   0.7825370 -0.55609410  0.67319733   0.56104069  1.0000000

	> correlations1 <- cor(M[,9:16])
	> print(correlations1)
    	     	 BsmtFinSF1  BsmtFinSF2  BsmtUnfSF TotalBsmtSF   X1stFlrSF   X2ndFlrSF LowQualFinSF   GrLivArea
	BsmtFinSF1    1.00000000  0.05794615 -0.3369508  0.64911517  0.62528283 -0.28441846  -0.21759921  0.24437901
	BsmtFinSF2    0.05794615  1.00000000 -0.3902274 -0.06358589 -0.06531842 -0.32276866   0.02471454 -0.30906664
	BsmtUnfSF    -0.33695078 -0.39022743  1.0000000  0.47120488  0.46528202  0.11854650  -0.07141840  0.45210860
	TotalBsmtSF   0.64911517 -0.06358589  0.4712049  1.00000000  0.97229358 -0.23299875  -0.25905954  0.55239198
	X1stFlrSF     0.62528283 -0.06531842  0.4652820  0.97229358  1.00000000 -0.19329001  -0.21208710  0.60816998
	X2ndFlrSF    -0.28441846 -0.32276866  0.1185465 -0.23299875 -0.19329001  1.00000000   0.09472676  0.65978628
	LowQualFinSF -0.21759921  0.02471454 -0.0714184 -0.25905954 -0.21208710  0.09472676   1.00000000 -0.03758891
	GrLivArea     0.24437901 -0.30906664  0.4521086  0.55239198  0.60816998  0.65978628  -0.03758891  1.00000000

	> correlations2 <- cor(M[,17:24])
	> print(correlations2)
    	     	BsmtFullBath BsmtHalfBath   FullBath   HalfBath BedroomAbvGr KitchenAbvGr TotRmsAbvGrd Fireplaces
	BsmtFullBath    1.0000000  -0.20541362 -0.0652792 -0.1542255  -0.38789072  -0.21741148   -0.1447363  0.2468905
	BsmtHalfBath   -0.2054136   1.00000000 -0.3481238 -0.1254230  -0.08656331  -0.02729678   -0.2694118 -0.1534202
	FullBath       -0.0652792  -0.34812385  1.0000000  0.3825315   0.40962501  -0.02838370    0.7620008  0.4718709
	HalfBath       -0.1542255  -0.12542298  0.3825315  1.0000000   0.43598312  -0.13817482    0.5444692  0.3105448
	BedroomAbvGr   -0.3878907  -0.08656331  0.4096250  0.4359831   1.00000000   0.30915930    0.8025793  0.2451442
	KitchenAbvGr   -0.2174115  -0.02729678 -0.0283837 -0.1381748   0.30915930   1.00000000    0.1794536 -0.2804036
	TotRmsAbvGrd   -0.1447363  -0.26941183  0.7620008  0.5444692   0.80257933   0.17945361    1.0000000  0.5720233
	Fireplaces      0.2468905  -0.15342018  0.4718709  0.3105448   0.24514423  -0.28040360    0.5720233  1.0000000

	> correlations3 <- cor(M[,25:32])
	> print(correlations3)
          	     	GarageYrBlt GarageCars GarageArea WoodDeckSF OpenPorchSF EnclosedPorch  X3SsnPorch ScreenPorch
	GarageYrBlt     1.0000000  0.8698746  0.84259150  0.5387439  0.56573977   -0.67266924 -0.02663060 -0.20770737
	GarageCars      0.8698746  1.0000000  0.97885295  0.5270854  0.60328408   -0.53818900 -0.06880340 -0.11983281
	GarageArea      0.8425915  0.9788529  1.00000000  0.5211545  0.61155539   -0.51001867 -0.06906734 -0.10693649
	WoodDeckSF      0.5387439  0.5270854  0.52115453  1.0000000  0.30164088   -0.42545359 -0.16749206 -0.22693486
	OpenPorchSF     0.5657398  0.6032841  0.61155539  0.3016409  1.00000000   -0.44677009 -0.11551430  0.08170367
	EnclosedPorch  -0.6726692 -0.5381890 -0.51001867 -0.4254536 -0.44677009    1.00000000 -0.05846854 -0.06526839
	X3SsnPorch     -0.0266306 -0.0688034 -0.06906734 -0.1674921 -0.11551430   -0.05846854  1.00000000 -0.11802191
	ScreenPorch    -0.2077074 -0.1198328 -0.10693649 -0.2269349  0.08170367   -0.06526839 -0.11802191  1.00000000

	> correlations4 <- cor(M[,33:36])
	> print( correlations4)
       	  PoolArea     MiscVal     MoSold      YrSold
	PoolArea  1.0000000  0.15798969 -0.1813847 -0.16211548
	MiscVal   0.1579897  1.00000000 -0.0269147  0.04271006
	MoSold   -0.1813847 -0.02691470  1.0000000 -0.33214935
	YrSold   -0.1621155  0.04271006 -0.3321493  1.00000000
 	
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Corrplot.PNG)



![](htps://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/CorrelationValuesplot01.png)
	
# Outliers detection

```{r}

## MSSubClass
m<- mean(train$MSSubClass)
std <- sd(train$MSSubClass)
ggplot()+
geom_histogram (data=train, aes(x=MSSubClass, y=..density..), fill="red")+ 
stat_function(fun=dnorm, args =list (mean =m, sd =std), aes (x=train$MSSubClass))
boxplot(train_num$MSSubClass)$out

## LotFrontage
m<- mean(train$LotFrontage)
std <- sd(train$LotFrontage)
ggplot()+
geom_histogram (data=train, aes(x=LotFrontage, y=..density..), fill="red")+ 
stat_function(fun=dnorm, args =list (mean =m, sd =std), aes (x=train$LotFrontage))
boxplot(train_num$LotFrontage)$out

## LotArea
m<- mean(train$LotArea)
std <- sd(train$LotArea)
ggplot()+
 geom_histogram (data=train, aes(x=LotArea, y=..density..), fill="red")+ 
 stat_function(fun=dnorm, args =list (mean =m, sd =std), aes (x=train$LotArea))
boxplot(train_num$LotArea)$out

## OverallQual
m<- mean(train$OverallQual)
std <- sd(train$OverallQual)
ggplot()+
geom_histogram (data=train, aes(x=OverallQual, y=..density..), fill="red")+ 
stat_function(fun=dnorm, args =list (mean =m, sd =std), aes (x=train$OverallQual))
boxplot(train_num$OverallQual)$out

## YearBuilt
 m<- mean(train$YearBuilt)
std <- sd(train$YearBuilt)
ggplot()+
geom_histogram (data=train, aes(x=YearBuilt, y=..density..), fill="red")+ 
stat_function(fun=dnorm, args =list (mean =m, sd =std), aes (x=train$YearBuilt))
boxplot(train_num$YearBuilt)$out

## GRLivArea
m<- mean(train$GrLivArea)
std <- sd(train$GrLivArea)
ggplot()+
geom_histogram (data=train, aes(x=GrLivArea, y=..density..), fill="red")+ 
stat_function(fun=dnorm, args =list (mean =m, sd =std), aes (x=train$GrLivArea))
boxplot(train_num$GrLivArea)$out

![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Oultiers_GrLivArea.png)
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Oultiers_GrLivArea.png)

## OverallCond
m<- mean(train$OverallCond)
std <- sd(train$OverallCond)
ggplot()+
geom_histogram (data=train, aes(x=OverallCond, y=..density..), fill="red")+ 
stat_function(fun=dnorm, args =list (mean =m, sd =std), aes (x=train$OverallCond))
boxplot(train_num$OverallCond)$out
```
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Oultiers_GrLivArea.png)
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Outliers_LotFrontage.png)
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Outliers_LotArea.png)
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Outliers_OverallCond.png)

# Variables with low variance

Zero variance in a regression model could cause the model fit to be unstable. Using caret package "nearZeroVar we looked for variables that have zero or  near zero variance

zv: remove attributes with a zero variance (all the same value).

nzv: remove attributes with a near zero variance (close to the same value).


```{r}
install.packages("caret")
names(train)[nearZeroVar(train)]

```

	[1] "Street"        "LandContour"   "Utilities"     "LandSlope"     "Condition2"    "RoofMatl"      "BsmtCond"      			    "BsmtFinType2" 
	[9] "BsmtFinSF2"    "Heating"       "LowQualFinSF"  "KitchenAbvGr"  "Functional"    "GarageQual"    "GarageCond"    			    "EnclosedPorch"
	[17] "X3SsnPorch"    "ScreenPorch"   "PoolArea"      "MiscVal"     

# Plot variables with low variance

```{r}
barplot(table(train$Street),ylab="Frequency",col="green",border="blue",main= "Street")
barplot(table(train$LandContour),ylab="Frequency",col="green",border="blue",main= "LandContour")
barplot(table(train$LandSlope),ylab="Frequency",col="green",border="blue",main= "LandSlope")
barplot(table(train$Condition2),ylab="Frequency",col="green",border="blue",main= "Condition2")
barplot(table(train$RoofMatl),ylab="Frequency",col="green",border="blue",main= "RoofMatl")
barplot(table(train$BsmtCond),ylab="Frequency",col="green",border="blue",main= "BsmtCond")
barplot(table(train$BsmtFinType2),ylab="Frequency",col="green",border="blue",main= "BsmtFinType2")
barplot(table(train$BsmtFinSF2),ylab="Frequency",col="green",border="blue",main= "BsmtFinSF2")
barplot(table(train$Heating),ylab="Frequency",col="green",border="blue",main= "Heating2")
barplot(table(train$LowQualFinSF),ylab="Frequency",col="green",border="blue",main= "LowQualFinSF")
barplot(table(train$KitchenAbvGr),ylab="Frequency",col="green",border="blue",main= "KitchenAbvGr")
barplot(table(train$Functional),ylab="Frequency",col="green",border="blue",main= "Functional")
barplot(table(train$GarageQual),ylab="Frequency",col="green",border="blue",main= "GarageQual")
barplot(table(train$GarageCond),ylab="Frequency",col="green",border="blue",main= "GarageCond")
barplot(table(train$EnclosedPorch),ylab="Frequency",col="green",border="blue",main= "EnclosedPorch")
barplot(table(train$X3SsnPorch),ylab="Frequency",col="green",border="blue",main= "X3SsnPorch")
barplot(table(train$ScreenPorch),ylab="Frequency",col="green",border="blue",main= "ScreenPorch")
barplot(table(train$PoolArea),ylab="Frequency",col="green",border="blue",main= "PoolArea")
barplot(table(train$iscVal),ylab="Frequency",col="green",border="blue",main= "MiscVal")

```
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Low_Variance_Variables.png)

![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Low_Variance_Variables1.png)

![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Low_Variance_Variables2.png)

# Removing highly correlated numeric variables
After the data imputation was performed we calculated the Pearson correlation between the numeric variables using the correlations matrix we could identify the variables that show high correlation.

![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Correlation_After_Imputation_Numeric2.PNG)

We can see that same variables have a strong positive correlation for example BasementHalfBath and BasementFullBath are hihgly correlated therefore we removed BasmentHalfBath. Similarly, GarageCars and GarageArea have a high correlation of those two we removed GarageCars

```
rmvVars <- c('BsmtHalfBath', 'GarageCars')
TestingSet <- TestingSet[,!(names(TestingSet) %in% rmvVars)]
TrainingSet <- TrainingSet[,!(names(TrainingSet) %in% rmvVars)]
```

# Histogram of the dependent variable 

The target variable (Sale Price) histogram shows that it is positively skewed (right skewed), because there is a long tail on the right side 

```{r}
library(ggplot2)
format(x,scientific=F)
options(scipen=999)
ggplot(train, aes(SalePrice))+geom_histogram(color="black",fill = "steelblue")

```

```{r}

require(ggplot2)
#adding normal distribution curve
m<- mean(train$SalePrice)
std <- sd(train$SalePrice)

ggplot()+
geom_histogram (data=train, aes(x=SalePrice, y=..density..), fill="red")+
stat_function(fun=dnorm, args =list (mean =m, sd =std), aes (x=train$SalePrice))

```
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/SalePrice_Distribution.png)

SalePrice in Function of the Neighboorhood

```{r}
ggplot(train, aes(reorder(x= district, -price), y=SalePrice, color = Neighborhood))+geom_boxplot() + labs(title = "Prices In Function Of The Neighborhood", y =" SalePrice")+coord_flip() 
```
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Price_Vs_Neighborhood.png)

SalePrice in Function of Zoning Classification
 
 The prices are  higher in zones located in FV (Floating Village Residential)
```{r}
ggplot(train, aes(reorder(x= MSZoning, -SalePrice), y=SalePrice, color = MSZoning))+geom_boxplot() + labs(title = "Prices In Function Of Zoning Classification", y =" SalePrice")+coord_flip() 
```
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Price_Vs_Zoning_Classfication.png)

SalePrice and YearBuilt

```{r}

ggplot(train, aes(x=YearBuilt, y=SalePrice, group=YearBuilt)) + geom_boxplot() + ggtitle("train~YearBuilt ") + xlab("YearBuilt") 	 + ylab("SalePrice")

```

SalePrice and GrLivArea

```{r}
ggplot(train, aes(x=GrLivArea, y=SalePrice, group=YearBuilt)) + geom_boxplot() + ggtitle("train~GrLivArea ") + xlab("GrLivArea") 	 + ylab("SalePrice") 

```

# Quantile-Quantile Plot (Plot/Line) dependent variable

QQPlot compares the quartiles of the datset with the ideal theoretical normal distribution. We can see that the lower left and 
upper rigth some data points fall a bit off the line. If they both came from the same distribution,we should see the points forming a line that is roughly straight. However, for prices that are far away from the average price, the plot deviates heavily from the qq line.

```{r}
	qqnorm(train$SalePrice)
	qqline(train$SalePrice)
```
	
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/QQplotOrig.PNG)

Skewness of the Dependent Variable

	Skewness is a measure of symmetry where distributions with 0 skew follow a normal distribution.For skewnesses outside the range 	of -0.8 to 0.8 do not satisfy the assumption of normality.To verify the central tendency we calculated this metric, the value 	      obtained  for skewness was 1.88 (right skew distribution) which is considered high. That means that values tend to concentrate	    to the left. The low values are more frequent than the high values and the Q-Q plot shows that sale prices are also not                 normally distributed. 

	```{r}
	install.packages("e1071")
	library(e1071)
	skewness(TrainingSet$SalePrice)
	```
	[1] 1.88

Calculating Kurtosis

	Kurtosis measures the taildness of the distribution kurtosises outside the range  of -3.0 to 3.0 do not satisfy the assumption 		of normality.The calculated value of Kurtosis is 6.5 that signifies that the majority of the values are concentrated around            the mean.This high value means that the distribution is too peak to be considered normal the curve is taller and skinier than          a normal distribution.
	
	```
	kurtosis(TrainingSet$SalePrice)
	[1] 6.5
	```
	
Log transformation
	To deal with the high skewness value we transformed to log10 the target variable. By performing  the logarithmic transformation 	we intend to remove and deal with the skewness therefore  increase the accuracy of the models that will be created.
	Because of this positive skew we might expect our model to overpredict our values. by transforming  to log the Sale Price we 	     migth get closer to normallity of the curve
	
	```{r}
	logTrainingSet$SalePrice <- log(logTrainingSet$SalePrice) 

Skewness with the log transformation
	
	```
	skewness(logTrainingSet$SalePrice)
	[1] -0.0203
	we were able to modify the curve and the skew is now quite low and the Q-Q plot is modified.
	```
Q-Qplot with Log Transformation

	qqnorm(logTrainingSet$SalePrice)
	
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/LogTransf_SalePrice.PNG)



#REGRESSION ANALYSIS

	```{r}
	install.packages("RCurl")
	install.packages("MASS")
	install.packages("Leaps")
	library(Rcurl)
	library(MASS)
	library(leaps)
	library(mlbench) 
	library(caret)
	```
Multivariate Linear Regression first run TrainingSet

	```{r}
	Model1 <- lm (SalePrice ~ ., data =logTrainingSet)
	Call:
	lm(formula = SalePrice ~ ., data = logTrainingSet)

	Residuals:
     	Min       1Q   Median       3Q      Max 
	-0.72648 -0.04784  0.00216  0.05056  0.72648 

	Coefficients: (20 not defined because of singularities)
                                      Estimate   Std. Error t value             Pr(>|t|)    
	(Intercept)                        8.734953717  4.921776685   1.775             0.076190 .  
	MSSubClass1 story 1945_ older     -0.005320876  0.069576495  -0.076             0.939054    
	MSSubClass1 story 1946+            0.076560671  0.068767822   1.113             0.265792    
	MSSubClass1 story pud_46_newer     0.015977582  0.096943287   0.165             0.869118    
	MSSubClass1,5 story finish         0.068875478  0.079469626   0.867             0.386284    
	MSSubClass1,5 story_unfinish      -0.159674085  0.131285462  -1.216             0.224133    
	MSSubClass2 family_conversion_all  0.186311993  0.149305067   1.248             0.212324    
	MSSubClass2 story_45_older         0.093406985  0.077361354   1.207             0.227510    
	MSSubClass2 story_46_newer         0.053576680  0.078350979   0.684             0.494230    
	MSSubClass2,5 story                0.034538384  0.098686083   0.350             0.726413    
	MSSubClassduplex_allstyles_age     0.053280578  0.077308365   0.689             0.490833    
	MSSubClasspud_ multilevel         -0.012308949  0.114996969  -0.107             0.914777    
	MSSubClasssplit foyer              0.082493356  0.085256951   0.968             0.333445    
	MSSubClasssplit/multilevel         0.000485662  0.090707013   0.005             0.995729    
	MSSubClassstory pud_46_newer      -0.059325374  0.107935465  -0.550             0.582670    
	StreetPave                         0.116275023  0.060543965   1.921             0.055029 .  
	LotShapeIR2                        0.021841338  0.019735608   1.107             0.268645    
	LotShapeIR3                        0.023914940  0.041488632   0.576             0.564438    
	LotShapeReg                        0.012075533  0.007551586   1.599             0.110066    
	LandContourHLS                     0.034327565  0.024356337   1.409             0.158978    
	LandContourLow                     0.015281776  0.030596317   0.499             0.617543    
	LandContourLvl                     0.027105576  0.017451812   1.553             0.120645    
	LotConfigCulDSac                   0.037126451  0.015499844   2.395             0.016759 *  
	LotConfigFR2                      -0.029443081  0.018947975  -1.554             0.120473    
	LotConfigFR3                      -0.080765731  0.058900757  -1.371             0.170562    
	LotConfigInside                   -0.015968942  0.008420306  -1.896             0.058135 .  
	LandSlopeMod                       0.039638430  0.018885415   2.099             0.036034 *  
	LandSlopeSev                      -0.196622359  0.053262661  -3.692             0.000233 ***
	NeighborhoodBlueste                0.044708654  0.092779051   0.482             0.629976    
	NeighborhoodBrDale                -0.021097948  0.054770348  -0.385             0.700152    
	NeighborhoodBrkSide                0.020378022  0.044959956   0.453             0.650451    
	NeighborhoodClearCr                0.012874782  0.043691741   0.295             0.768294    
	NeighborhoodCollgCr               -0.021783277  0.034143228  -0.638             0.523597    
	NeighborhoodCrawfor                0.095092923  0.040663916   2.339             0.019523 *  
	NeighborhoodEdwards               -0.096965698  0.037925434  -2.557             0.010687 *  
	NeighborhoodGilbert               -0.016717446  0.036314037  -0.460             0.645342    
	NeighborhoodIDOTRR                -0.016137379  0.050702013  -0.318             0.750328    
	NeighborhoodMeadowV               -0.154587817  0.057751466  -2.677             0.007534 ** 
	NeighborhoodMitchel               -0.062025747  0.038544761  -1.609             0.107837    
	NeighborhoodNAmes                 -0.044069428  0.036943286  -1.193             0.233145    
	NeighborhoodNoRidge                0.035496720  0.040019859   0.887             0.375268    
	NeighborhoodNPkVill                0.012546654  0.065557453   0.191             0.848257    
	NeighborhoodNridgHt                0.079106325  0.035203218   2.247             0.024812 *  
	NeighborhoodNWAmes                -0.034056753  0.038033334  -0.895             0.370728    
	NeighborhoodOldTown               -0.034359484  0.045341750  -0.758             0.448725    
	NeighborhoodSawyer                -0.035139307  0.038327354  -0.917             0.359420    
	NeighborhoodSawyerW               -0.013956306  0.036928222  -0.378             0.705549    
	NeighborhoodSomerst                0.038234025  0.042618199   0.897             0.369829    
	NeighborhoodStoneBr                0.119888369  0.039676831   3.022             0.002567 ** 
	NeighborhoodSWISU                 -0.012758289  0.045909977  -0.278             0.781138    
	NeighborhoodTimber                 0.011994630  0.038535416   0.311             0.755655    
	NeighborhoodVeenker                0.056940274  0.049573849   1.149             0.250951    
	Condition1Feedr                    0.027814441  0.023436548   1.187             0.235541    
	Condition1Norm                     0.081794369  0.019551519   4.184 0.000030779622290172 ***
	Condition1PosA                     0.040634961  0.046716834   0.870             0.384575    
	Condition1PosN                     0.091104600  0.034873041   2.612             0.009101 ** 
	Condition1RRAe                    -0.028383759  0.042477922  -0.668             0.504134    
	Condition1RRAn                     0.043141361  0.031875017   1.353             0.176164    
	Condition1RRNe                     0.006244182  0.081703767   0.076             0.939094    
	Condition1RRNn                     0.036561530  0.059977375   0.610             0.542249    
	Condition2Feedr                    0.198916299  0.124783534   1.594             0.111177    
	Condition2Norm                     0.170004885  0.112295342   1.514             0.130311    
	Condition2PosA                     0.399279337  0.184039384   2.170             0.030237 *  
	Condition2PosN                    -0.671315594  0.143227971  -4.687 0.000003086794699893 ***
	Condition2RRAe                    -0.399902249  0.237355324  -1.685             0.092281 .  
	Condition2RRAn                     0.076274750  0.160129230   0.476             0.633924    
	Condition2RRNn                     0.185300106  0.141607902   1.309             0.190938    
	BldgType2fmCon                    -0.132780605  0.131194695  -1.012             0.311699    
	BldgTypeTwnhs                     -0.025871487  0.073225349  -0.353             0.723915    
	BldgTypeTwnhsE                     0.016589982  0.069551432   0.239             0.811512    
	HouseStyle1.5Unf                   0.200017799  0.111041247   1.801             0.071906 .  
	HouseStyle1Story                  -0.008479783  0.042146931  -0.201             0.840579    
	HouseStyle2.5Fin                  -0.034076382  0.081230408  -0.420             0.674923    
	HouseStyle2.5Unf                   0.062634891  0.077990127   0.803             0.422067    
	HouseStyle2Story                   0.000267864  0.038791435   0.007             0.994492    
	HouseStyleSFoyer                  -0.003852269  0.055758946  -0.069             0.944931    
	HouseStyleSLvl                     0.078066648  0.065105040   1.199             0.230729    
	OverallQual2                       0.596506782  0.150204296   3.971 0.000075705822579773 ***
	OverallQual3                       0.665657919  0.139696964   4.765 0.000002117763324022 ***
	OverallQual4                       0.703324764  0.139289381   5.049 0.000000511427747420 ***
	OverallQual5                       0.741054628  0.139641825   5.307 0.000000132637833644 ***
	OverallQual6                       0.774780877  0.139874341   5.539 0.000000037283452407 ***
	OverallQual7                       0.810492483  0.139856896   5.795 0.000000008702655490 ***
	OverallQual8                       0.870406750  0.140425713   6.198 0.000000000781790667 ***
	OverallQual9                       0.935960538  0.142905644   6.550 0.000000000085233166 ***
	OverallQual10                      0.933617651  0.146741880   6.362 0.000000000281546026 ***
	OverallCond2                      -0.779504221  0.258445032  -3.016             0.002614 ** 
	OverallCond3                      -0.928853150  0.237909402  -3.904 0.000099756880492195 ***
	OverallCond4                      -0.848913747  0.240633387  -3.528             0.000435 ***
	OverallCond5                      -0.809741099  0.239875722  -3.376             0.000760 ***
	OverallCond6                      -0.774866596  0.239595787  -3.234             0.001254 ** 
	OverallCond7                      -0.739733454  0.239952169  -3.083             0.002097 ** 
	OverallCond8                      -0.723454909  0.239681157  -3.018             0.002594 ** 
	OverallCond9                      -0.673963457  0.241128848  -2.795             0.005272 ** 
	RoofStyleGable                    -0.032995506  0.085776587  -0.385             0.700551    
	RoofStyleGambrel                  -0.047633360  0.093902914  -0.507             0.612064    
	RoofStyleHip                      -0.035616551  0.085938572  -0.414             0.678624    
	RoofStyleMansard                   0.025968482  0.100089501   0.259             0.795330    
	RoofStyleShed                      0.491252175  0.174946036   2.808             0.005065 ** 
	RoofMatlCompShg                    2.694187993  0.153480256  17.554 < 0.0000000000000002 ***
	RoofMatlMembran                    3.060477398  0.221127229  13.840 < 0.0000000000000002 ***
	RoofMatlMetal                      2.923389686  0.216900946  13.478 < 0.0000000000000002 ***
	RoofMatlRoll                       2.702118004  0.194430873  13.898 < 0.0000000000000002 ***
	RoofMatlTar&Grv                    2.682225756  0.175170400  15.312 < 0.0000000000000002 ***
	RoofMatlWdShake                    2.610871523  0.170615093  15.303 < 0.0000000000000002 ***
	RoofMatlWdShngl                    2.736759059  0.159112291  17.200 < 0.0000000000000002 ***
	ExterQualFa                        0.124643132  0.062458763   1.996             0.046201 *  
	ExterQualGd                       -0.004150892  0.024530928  -0.169             0.865659    
	ExterQualTA                       -0.007724441  0.026574618  -0.291             0.771354    
	ExterCondFa                       -0.064261708  0.087538326  -0.734             0.463032    
	ExterCondGd                       -0.041581207  0.083518508  -0.498             0.618668    
	ExterCondPo                       -0.287753633  0.170732957  -1.685             0.092170 .  
	ExterCondTA                       -0.023917911  0.083675482  -0.286             0.775049    
	FoundationCBlock                  -0.011441616  0.015225247  -0.751             0.452504    
	FoundationPConc                    0.019322747  0.016368255   1.181             0.238033    
	FoundationSlab                    -0.022291676  0.038635207  -0.577             0.564062    
	FoundationStone                    0.074060541  0.051974403   1.425             0.154432    
	FoundationWood                    -0.131656485  0.069043197  -1.907             0.056774 .  
	HeatingGasA                        0.190837705  0.119209541   1.601             0.109670    
	HeatingGasW                        0.256857289  0.123246808   2.084             0.037362 *  
	HeatingGrav                       -0.079758155  0.131498309  -0.607             0.544274    
	HeatingOthW                        0.167067282  0.147461659   1.133             0.257459    
	HeatingWall                        0.228416211  0.137764723   1.658             0.097574 .  
	HeatingQCFa                       -0.006574916  0.022359979  -0.294             0.768771    
	HeatingQCGd                       -0.015118181  0.009706361  -1.558             0.119601    
	HeatingQCPo                       -0.062926356  0.124499081  -0.505             0.613345    
	HeatingQCTA                       -0.027028393  0.009689552  -2.789             0.005363 ** 
	CentralAirY                        0.061091257  0.018408396   3.319             0.000931 ***
	PavedDriveP                        0.000838318  0.025808561   0.032             0.974093    
	PavedDriveY                        0.031599897  0.016156505   1.956             0.050712 .  
	SaleConditionAdjLand               0.092780323  0.067985428   1.365             0.172599    
	SaleConditionAlloca                0.067820425  0.040979449   1.655             0.098187 .  
	SaleConditionFamily                0.024917054  0.028514409   0.874             0.382379    
	SaleConditionNormal                0.074327132  0.013453115   5.525 0.000000040351531307 ***
	SaleConditionPartial               0.025166150  0.069782559   0.361             0.718434    
	```
	Residual standard error: 0.1059 on 1207 degrees of freedom
	Multiple R-squared:  0.9419,	Adjusted R-squared:  0.9297 
	F-statistic:  77.6 on 252 and 1207 DF,  p-value: < 0.00000000000000022
	
	Prediction in the TestingSet

	```{r}
	prediction <-predict (model1,interval="prediction",newdata = TestingSet)
	prediction
	summary(prediction)
	str(prediction)
	head(prediction)
	```
Calculating  Error

	```{r}
	errors <- prediction [,"fit"]-TestingSet$SalePrice
	errors
	str(errors)
	summary(errors)
	```
Ploting the errors
	```{r}
	#plot errors
	format(x,scientific=F)
	hist(errors)
![](https://github.com/JvaSar/House-Sale-Price-Prediction/blob/master/Hist_errors_1.PNG)

Backward elimination
```{r}
full<-lm(SalePrice~ ., data=LogTrainingSet)
stepB <-stepAIC(full,direction="backward",trace=TRUE)
```
```{r}

summary(stepB)

	```{r}
	TrainingSet <- train
	TestingSet <- newtest

	Model <- train(SalePrice ~ ., data = TrainingSet,
               method = "lm",
               na.action = na.omit,
               preProcess=c("scale","center"),
               trControl= trainControl(method="none")
	```
Results 
	
	```{r}
	summary(Model)
	```

Apply model for prediction

	```{r}
	Model.training <-predict(Model, TrainingSet) # Apply model to make prediction on Training set
	Model.testing <-predict(Model, TestingSet) # Apply model to make prediction on Testing set
	summary(Model.training)
	summary(Model.testing)
	```
	Model performance (Displays scatter plot and performance metrics)
	
	```{r}
	# Scatter plot of Training set
	format(x,scientific=F)
	plot(TrainingSet$SalePrice,Model.training, col = "blue")
	plot(TestingSet$SalePrice,Model.testing, col = "blue")
	```

Evaluating the model

	RMSE (root mean square error) and R2 (regression were the metrics used to evaluate the regression model for that we use caret 	      	 
	package. RMSE or Root Mean Squared Error represent the average deviation of the predictions from the observations. The values             
	of the error ideally should be normaly distributed
