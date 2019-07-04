---
layout: post
published: true
title: 'Lesson 9: Rise of TSVer'
subtitle: TSVing and the like
date: '2019-06-04'
---
## Finally getting around to putting these on the internet.

My own code on this was a dumpster fire, so I went to look at both the solutions offered by Maxim and Berni, and annotated apropriately.  Seemed like a good way to learn from those who are in the know, you know?

    #import necessary chunks to actually run everything else
    import re, os, csv

    #a source for the xml files
    source = "C:/Users/Liam/Documents/School/GradSchool/DHMethods/Lesson08/betterdispatch/"
    #a source for the xml files for test purposes
    #source = "C:/Users/Liam/Documents/School/GradSchool/DHMethods/Lesson08/testdispatch/"
    #folder for the resulting TSV
    target = "C:/Users/Liam/Documents/School/GradSchool/DHMethods/Lesson09/tsvdispatchX/"

    #create a list of files from the source
    lof = os.listdir(source)

    #Let's make an empty list!
    list = []

    #this is a loop of files in the list of files
    for f in lof:
        if f.startswith("dltext"): # fileName test
            #opening some files using the proper encoding and making it into the variable "f1"
            with open(source + f, "r", encoding="utf8") as f1:
                #the "text" is the contents of f1
                text = f1.read()

                #look for the date by searching for digits following the correct xml tag
                date = re.search(r'<date value="([\d-]+)"', text).group(1)

                # splitting the issue into articles/items using xml tags
                split = re.split("<div3 ", text)

                c = 0 # item counter
                #we enter into another loop of items and ditch the tag
                for s in split[1:]:
                    #keep the count going
                    c += 1
                    s = "<div3 " + s # a step to restore the integrity of items
                    #input(s) (by keeping tags around)

                    # try to find a unitType using regular expressions
                    try:
                        unitType = re.search(r'type="([^\"]+)"', s).group(1)
                    #if it doesn't work, tell us, and specify this as having no unit type
                    except:
                        unitType = "noType"
                        print(s)

                    # try to find a header using XML tags. If that doesn't work, YELL THAT THERE ISN'T A HEADER.
                    try:
                        header = re.search(r'<head>(.*)</head>', s).group(1)
                        header = re.sub("<[^<]+>", "", header)
                    except:
                        header = "NO HEADER"
                        print("\nNo header found!\n")


                    #a number of text cleanup options.  #replace xml tags, new lines, empty text, tabs to clean up the output
                    text = re.sub("<[^<]+>", "", s)
                    text = re.sub(" +\n|\n +", "\n", text)
                    text = re.sub("\n+", ";;; ", text)
                    text = re.sub("\t+", " ", text)

                    # generating necessary bits
                    fName = date+"_"+unitType+"_"+str(c)

                    # creating a dictionary with all of the following fun details
                    dict = {
                        'id': fName,
                        'date': date,
                        'type': unitType,
                        'header': header,
                        'text': text
                    }
                    # appending the dict to a list
                    if re.search("[\w]+", text):
                        list.append(dict)


      #column names for the tsv
        csv_columns =  ['id', 'date', 'type', 'header', 'text']

      #actually write the TSV.  Open up the file in the target area, then use dictwriter to write out the contents of the dictionaries.
      #break into chunks on tab, using the delimiter.  Write it all down.
        with open(target+'dipatch.tsv', 'w', encoding="utf8") as f:
            writer = csv.DictWriter(f, delimiter ='\t',fieldnames=csv_columns)
            writer.writeheader()
            for data in list:
                writer.writerow(data)
          
And the other one.

    import re, os

    #Original files
    source = "C:/Users/Liam/Documents/School/GradSchool/DHMethods/Lesson08/betterdispatch/"

    #a source for the xml files, for test purposes
    #source = "C:/Users/Liam/Documents/School/GradSchool/DHMethods/Lesson08/testdispatch/"

    #Where the files will be written
    target = "C:/Users/Liam/Documents/School/GradSchool/DHMethods/Lesson09/tsvdispatch/"

    #Establish a list of files
    lof = os.listdir(source)

    #Keep count of progress
    counter = 0 

    #create a blank list to which things will be added
    ourCSV = []

    #a loop. For files in the previously made list of files
    for f in lof:
        #tests whether or not this is a file of the type we need - all of our dispatches start with dltext
        if f.startswith("dltext"): 
            #select and individual tartget file, sets "text" as the contents of that file        
            with open(source + f, "r", encoding="utf8") as f1:
                text = f1.read()

                # find the date using regular expressions - looking for proper xml tag
                date = re.search(r'<date value="([\d-]+)"', text).group(1)

                # splitting the issue into articles/items on the xml marker
                split = re.split("<div3 ", text)

                #item counter to help with writing of files
                c = 0

                #sub-loop, going through articles/items from a specific file
                for s in split[1:]:


                    c += 1
                    s = "<div3 " + s # a step to restore the integrity of items
                    #input(s)

                    # try to find a unitType
                    try:
                        unitType = re.search(r'type="([^\"]+)"', s).group(1)
                    except:
                        unitType = "noType"
                        print(s)

                    # try to find a header, as well as removing formatting to clarify it
                    try:
                        header = re.search(r'<head>(.*)</head>', s).group(1)
                        header = re.sub("<[^<]+>", "", header)

                    #when there's no header, write as much
                    except:
                        header = "NO HEADER"

                    #replace xml tags, new lines, empty text, tabs to clean up the output
                    text = re.sub("<[^<]+>", "", s)
                    text = re.sub(" +\n|\n +", "\n", text)
                    text = re.sub("\n+", ";;; ", text)
                    text = re.sub("\t+", " ", text)

                    itemID = date+"_"+unitType+"_"+str(c)

                    #test if the file is empty to skip writing it
                    if text == ";;;":
                        pass
                    else:
                        # creating a text variable
                        var = "\t".join([itemID,date,unitType,header,text])
                        #input(var)

                        ourCSV.append(var)

            #count processed issues and print progress counter at every 100        
            counter += 1
            if counter % 100 == 0:
                print(counter)

    #saving to a location - the target listed above, plus this filename. Easier to change the target around than have the full name here.
    with open(target+"dispatch_as_TSV.csv", "w", encoding="utf8") as f9:
        f9.write("\n".join(ourCSV))
    print(counter)


Also, the Codecademy screenshots
These were a helpful learning tool, but I was certainly not really prepared for the kinds of scripts we need now...

![Here it is!]({{site.baseurl}}/img/lastpython.PNG)


