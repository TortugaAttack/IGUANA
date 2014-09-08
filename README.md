#Mosquito
========
Mosquito is both a program and an API framework for benchmarking SPARQL Endpoints.
On one side it provides the ability to benchmark different SPARQL endpoints (e.g. triplestores) with 
given or generated datasets and predefined test cases on the other side it provides a framework and API 
to implement either a complete different benchmark or just parts of the benchmark (e.g. own test cases).

[JavaDoc](http://aksw.github.io/mosquito/javadoc/)


#CHANGELOG:
==========
= v0.1.0 = (Initial)
==========

CORE FEATURES:
-----------------------------------------------------------------------------

+ Testcases interface for your own Tests
	(currently supported Queries: 
			SPARQL Select, Ask, Describe, Construct,
			Insert, Delete (others are experimental!)

	-> Testcases can be easily implemented using the MBF Jar file 
	   as a library. 
	   (use the Javadoc for better understanding of the functions)
	-> Several Testcases are implemented (can be used through config.xml)
		-> UploadTestcase
			-> How long will it take to upload the whole dataset
		-> Queries Per Second (QPS)
			-> Update and Non Update Queries will be 
			   seperated
			-> The seperation will be used to
			   have a seperation in the requests as follows
			   Update Query before X Non Update Queries
			   vica vers. 
		-> Query Mixes per Hour (QMpH) (The results for QPS will be tested herein)
		-> QPS and QMpH will be tested in the QueryTestcase 
		-> Stresstest 
			-> several users(threads) will Test QueryTestcase
		   	   at the same time to test a 
			   real world situation
	-> The Queries can be handled through the QueryHandler Class
	   (more in the Javadoc)
	-> Also every Testcase can work with LiveData Files 
	   (Every File will be used as 1 Update File 
	    Form must be: "[0-9]{6}(added|removed).nt")

+ Benchmark as a Program 
	-> Through the configuration in the config.xml you can start the MBF 
	   Jar file as a program and it will make a whole Benchmark of the 
	   given Triplestores with the given Datasets with a given Reference 
    	   Triplestore (RefTS, which will not be tested, but needed to change your 
	   Query Patterns) with QueryPatterns (see Manual) to test.
	   The QueryPatterns must be in a File seperated by Lines.
	   (NO QUERIES ARE ALLOWED TO BE FORMATED!!)
	   The Query Patterns will be dynamically changed through the RefTS 
  	   If the Variables in the Patterns have no tuple answer they won't be tested
	  
	WORKFLOW (Steps in brackets are optional, Steps like [a | b] you can choose between either a or b):
		
	0. (Convert nonRDF Data into RDF Data)
	1. [Get your Virtuoso Log File and Cluster it to find the QueryPatterns (see LogClustering)| 
	    Get your QueryPattern File]
	2. With the RefTS dynamically change the Query Patterns to Queries
	3. for every selected Testcase & Triplestore:
		3.1. warmup the Triplestore with given Queries (disjunct from your teste Queries) 
	 	3.2. Start the Testcase
		3.3. Get the Results (Save them temporarly) 
		3.4. Union the Results with the Results of the Triplestores tested until now
	4. Save the Results as CSV Data and BarCharts as a pgn file
	5. (Send Success Email if given) 
	
	-> If the Benchmark aborts at some time and you give your Email the Benchmark tries to 
	   send you that something went wrong with an Exception

+ LogFiles (currently only very important parts will be logged)

+ LogClustering
	-> Get Queries out of LogFiles and clusters them
	-> 3 Strategies for 2 Algorithm
	-> Algorithms: BorderFlow, SortedStructureClustering (Cluster will be the frequent Querystructures)
	-> Strategies: SortedStructureClustering, BorderFlow: QuerySimilarity or StructureSimilarity (with levenshtein and features)
	-> will save the queries as querypatterns with Variables to ensure that these will not be cached

-----------------------------------------------------------------------------

ADDITIONAL FEATURES:
-----------------------------------------------------------------------------

+ Send Email if Succeed/Aborted 
	-> with CSV and PGN Results as Attachment if wished
	-> if aborted the the tempResults dir will be attached
	-> if succeded the attached directory structure will be as follows

+ Directory of the results has a clear structure 
	-> ./results/TestCaseName/

+ Converter interface for non RDF Data 
	-> Converters can be created without changing the Framework Code 
	   and can easily added through putting the Converter.jar 
	   into the lib Folder
		-> accessible over the class Name of the Converter
		   by putting it into the config.xml
	-> Implemented is an PGN to RDF Converter
	-> Will convert the non RDF Data into a given (config.xml) 
	   Format (TURTLE, RDF/XML, N3 are currently supported) 
	   before the Benchmark starts. 
	   And uses this Data in the Benchmark

+ LogCluster interface if your logfiles are messed up or you simply want to consider different aspects in your queries