# CivicBand: Amplifying Accountability in State and Local Government

Every day, State and Local governments across the US and Canada hold meetings and make decisions that affect millions of lives. **People have a right to know what’s happening in their governments, but exercising that right isn’t always easy:** the information is out there– it has to be, by law– but it can be difficult to find, and tedious to sift through. But with CivicBand, you can search through a database of more than 8 million pages (and counting\!) of records and meeting minutes from more than 630 municipalities across North America. 

CivicBand is a tool for citizens, activists, journalists, and anyone else that cares about regional issues and holding elected officials to account. We're just getting started, and we’d love for you to get involved. Check out how we’ve built CivicBand below, and reach out to [hello@civic.band](mailto:hello@civic.band) if you want to get more involved. But first, an overview: 

# **What is CivicBand?** 

**CivicBand is a collection of sites for querying and exploring municipal and civic data**. Each site is its own [Datasette](https://datasette.io/) instance, with both in-house and third-party plugins.

The way we get data from municipality websites and into our system follows a pretty standard **Extract, Transform, Load pattern:** First, we pull data (in the form of PDFs) out of websites. Then, we use an open-source Optical Character Recognition (OCR) that converts the PDFs into searchable text. Next, we upload the text into a SQLite DB which is deployed with Datasette to the production server. 

Right now, CivicBand is a combination of open- and closed-source code, and running it requires access to our repositories. We encourage others to build their own versions of CivicBand based on the architecture and tooling described below. If you’re interested in contributing to our version of CividBand, you can check out our [tracker](https://github.com/orgs/civicband/projects/1/views/1) on GitHub, or email us at [hello@civic.band](mailto:hello@civic.band). 

## CivicBand Architecture

The interface to the CivicBand pipeline is currently a command line application. Here’s what happens when it runs: 

1. You’ll be asked to create a new subdomain for the locality whose data you want to add to the database (for example, Queen Anne’s County).   
2. The user interface will automatically generate additional parameters, such as “State” and “Kind” that can be used by end-users to search the database. Enter the information as required.   
3. Next, run the scraper[^1] appropriate for whichever service provider is hosting the locality’s files. **Note: Not all localities use vendor systems for building and storing records.** (Ones who do will usually have the vendor name listed in the domain.)   
4. Provide the URL to scrape, and CivicBand will initiate the following Extract, Transform, and Load processes:  
   1. Create folders organized by “pdfs/MeetingName” into which the PDF files can be stored and organized by date (for example, pdfs/CityCouncil/2020-04-20.pdf")  
   2. Fetch and store the PDFs  
   3. Run all the PDFs through a program that splits each PDF into a folder of images by page number (for example,"images/CityCouncil/2020-04-20/1.png")  
   4. Upload the images to a plugin-defined CDN, enabling them to be displayed alongside the text result in the end user interface   
   5. Run all the images through the OCR and save the output as text files (for example, "txt/CityCouncil/2020-04-20/1.txt")   
   6. Create a SQLite database with “Full Text Search” turned on  
   7. Load all the text files as rows into the database   
   8. Deploy that database to a docker container running Datasette on the production server

Grab a cup of coffee (or your beverage of choice), since processing years’ worth of records can take a little while (even with the parallelized OCR processing we’ve implemented in the code). Depending on how many records there are, it could take anywhere from a few minutes to over a day.

## What’s next for CivicBand? 

We’re continuing to upload more data, and build out the tooling to be able to data from other sources (such as meeting minutes from School Boards, which typically use a different document management system). We encourage anyone interested in supporting our work to check out our [Task Tracker](https://github.com/orgs/civicband/projects/1/views/1) or [become an Advocate via our mailing list](https://buttondown.com/civicband/archive/will-you-be-an-advocate-for-civicband/).

[^1]:  CivicBand’s scrapers are currently closed-source. 
