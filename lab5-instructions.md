# Lab 5: ArcGIS Field Maps

## TGIS 504, Winter 2023, Dr. Emma Slager

### Introduction

While Lab 4 introduced you to Esri's form-centric data collection tool--Survey123--this lab will introduce you to Esri's map-centric data collection tool, Field Maps. Like Survey123, Field maps is available as a mobile app. Field teams can download surveys to the app on their mobile devices to use in the field and use them to update information about existing features or collect data about new features. If a data connection is available in the field, data can be uploaded immediately, or it can be stored to be uploaded when a connection to the Internet is restored. Field Maps supports any kind of vector feature--point, line, or polygon--and is integrated with ArcGIS Online services to support data storage and map display/interactivity.

Set up for Field Maps is a bit more complicated than for Survey123, and involves creating a geodatabase and an editable feature service with desktop GIS software rather than using a web interface. The general steps for this lab are as follows:

- Plan data to be collected
- Build a geodatabase and feature classes for your Field Maps instance
- Build and share a map to be used in the Field Maps instance
- Download the Field Maps app and your Field maps instance
- Collect sample data
- Write a brief reflective essay

*Technology stack for this lab:*

- ArcPro, available in the lab if you do not have it installed on your personal computer
- ArcGIS Online, accessed by computer
- ArcGIS Field Maps, downloaded to and accessed by mobile device
- Submission on Canvas

### Part 1: Identifying a data collection scenario and planning your data entry

For this lab, you'll be building a Field maps instance that could be used by Metro Parks Tacoma staff to create and maintain an inventory of parks, their features, and their maintenance needs. This will include:

- a polygon layer for each park with associated attributes like name, area, and the date of its last inventory survey
- a point layer for bathroom facilities with associated attributes about whether the bathroom needs to be cleaned or repaired
- a line layer for paths and associated attributes such as whether the path is paved or not
- three additional layers of your choosing and design

As you'll see in part 2 of the instructions, creating the geodatabase that will hold these layers is made much easier by having planned your data organization in schema tables ahead of time. See below for examples of the feature layers we'll create for the first three layers, then use the blank table template (available on GitHub as a Word doc) to plan out the additional layers you will create. You may find this documentation on from Esri helpful to read as you prepare to go about this: [Documentation: Design an editable layers](https://doc.arcgis.com/en/field-maps/android/help/design-an-editable-layer.htm). 

**Notes about schemas**

- The *feature class* can be point, line, or polygon and you should give it a descriptive name.
- The *field name* should have no spaces or special characters (underscores are OK). Each question in your survey represents a field. Each field will eventually be column in the feature class's attribute table.
- The *alias* should be a human readable version of the field name.
- The *type* can include:
  - Date—Date (and time)
  - Float – Numbers with fractional values (decimals), precise to six decimal places
  - Double—Numbers with fractional values (decimals), precise beyond six decimal places but requiring larger storage capacity than float fields
  - Short Integer—Whole numbers from -32,768 to 32,767
  - Long Integer—Whole numbers from -2,147,483,648 to 2,147,483,647, requiring larger storage capacity than short integer fields
  - Text/String—Any sequence of characters
  - Blob—Handles photos
- *Length* is the number of allowed characters in the field's value. This must be specified for any string/text field.
- *Allow Null* must be either true or false. If true, it means this field can be left blank.
- *Domain* is ArcMap's term for a list of choices for users to input data. Providing dropdown choices can be very useful for limiting user error and aiding standardization, but it can also be limiting, so you need to ensure you provide choices for the full range of possible answers. Not every field will utilize a domain, but those that do should be specified in the second Domain table. Domains can be re-used; for instance if you have multiple yes/no questions, they can all use the same YesNo domain.
- *Notes* are any additional information about the question you want to provide to the surveyor.
- NA stands for "Not Applicable"; ADA stands for "Americans with Disabilities Act"

| **Feature Class: ParkBoundaries, Feature type: polygon** |                     |          |            |            |                     |                |
| -------------------------------------------------------- | ------------------- | -------- | ---------- | ---------- | ------------------- | -------------- |
| **Field Name**                                           | **Alias**           | **Type** | **Length** | **Domain** | **Notes**           | **Allow Null** |
| ParkName                                                 | Name of the Park    | String   | 100        | NA         |                     | False          |
| ParkArea                                                 | Area of the park    | Float    | NA         | NA         | Calculated in acres | False          |
| LastSurvey                                               | Date of last survey | Date     | NA         | NA         |                     | True           |

| Feature Class: Bathrooms, Feature Type: point |                                             |          |            |               |                                                              |                |
| --------------------------------------------- | ------------------------------------------- | -------- | ---------- | ------------- | ------------------------------------------------------------ | -------------- |
| **Field Name**                                | **Alias**                                   | **Type** | **Length** | **Domain**    | **Notes**                                                    | **Allow Null** |
| GenderType                                    | Is the bathroom limited to certain genders? | String   | 50         | GenderOptions |                                                              | false          |
| StallType                                     | Is the bathroom multi-stall or single stall | String   | 50         | StallOptions  |                                                              | false          |
| ADA_Accessible                                | Is the bathroom ADA accessible?             | String   | 50         | YesNo         |                                                              | false          |
| NeedsCleaning                                 | Does the bathroom need to be cleaned?       | String   | 50         | YesNo         |                                                              | false          |
| NeedsSupplies                                 | Does the bathroom need supply refills?      | String   | 50         | YesNo         | Supplies include toilet paper, hand soap, or paper towels    | false          |
| NeedsRepairs                                  | Does the bathroom need repairs?             | String   | 50         | YesNo         | May include plumbing, electrical, or structural repairs      | false          |
| Photo                                         | NA                                          | Blob     | NA         | NA            | Attach a photo of any maintenance issues that need to be addressed | true           |

| **Feature Class: Paths, Feature Type: line** |                                 |          |            |                |                                                              |                |
| -------------------------------------------- | ------------------------------- | -------- | ---------- | -------------- | ------------------------------------------------------------ | -------------- |
| **Field Name**                               | **Alias**                       | **Type** | **Length** | **Domain**     | **Notes**                                                    | **Allow Null** |
| SurfaceType                                  | Type of path surface            | String   | 50         | SurfaceOptions |                                                              | false          |
| NeedsMaintenance                             | Does the path need maintenance? | String   | 50         | YesNo          | May include snow or debris removal or repaving.              | false          |
| ADA_Accessible                               | Is the path ADA accessible      | String   | 50         | YesNo          | Consult ADA guidelines regarding width, slope, etc.          | false          |
| Photo                                        | NA                              | Blob     | NA         | NA             | Attach a photo of any maintenance issues that need to be addressed | true           |

Domain Table:

| **Domain Name** | **Option List**                | Option Aliases                 |
| --------------- | ------------------------------ | ------------------------------ |
| GenderOptions   | AllGender, Men, Women          | All Gender, Men, Women         |
| StallOptions    | MultiStall, SingleStall        | Multi-Stall, Single Stall      |
| YesNo           | Yes, No, Unknown               | Yes, No, Unknown               |
| SurfaceOptions  | Paved, Dirt, Gravel, Woodchips | Paved, Dirt, Gravel, Woodchips |

Using the template provided, produce similar tables for the three additional feature layers you will include in your survey. Be sure to specify a feature type (point, line, or polygon). Your additional feature layers may be for features such as park benches, playgrounds, etc. You must decide what attributes to collect for each feature class. Add any dropdown options you will need to a domain in the Domain Table.

### Part 2: Build an editable feature layer to store the data your users will gather with Field Maps

Before we can start gathering data with Field Maps, we need to build the geodatabase and editable feature layers that will hold the data once it is collected. Your geodatabase should have a structure based on the data schema you mapped out in Part 1. It needs to include a feature class for every class of feature your user will collect (point, line, or polygon) as well as fields for all of the tabular information you want to collect about each feature. **Before you start, please double check that you are logged into ArcPro with your UWT credentials!** This is important so that you can share your lab products with the cohort and with me so that I can grade your work. 

Using your data schema from part 1, build your geodatabase, define your domains, and create your feature classes in ArcPro. Reference the directions found in [this documentation](https://doc.arcgis.com/en/field-maps/android/help/create-a-map.htm) (For this step, see especially the section called 'Prepare and editable layer'). We will also have practiced this together in class. A summary of the steps is included below: 

1. Create a new geodatabase, saving it in the location where you keep your lab files
2. In your geodatabase (using Catalog), create domains for all of the dropdown choices your user can utilize to fill in the attributes for feature class layers. 
3. In your geodatabase (using Catalog), create a feature class for each of the layers you defined in Part 1 above. You should create five of these from scratch (2 defined for you in part 1 above--bathrooms and paths--and three you planned and defined for yourself.)
4. Enable attachments for any feature class for which your user might want to take a photo. 
5. In your geodatabase, import the existing data we have for the ParkBoundaries layer, following these steps: 
   1. Open Catalog and navigate to the location where your geodatabase is stored. 
   2. Right-click on the geodatabase >> Import >> Feature class 
   3. In the window that appears enter the following: 
      * Input features: navigate to the location where the Parks layer you downloaded from GitHub is stored. You will need to extract this from the Zip you downloaded before it is available to access. 
      * Output location: this should by default already be set to the Geodatabase you set up for this lab, but confirm that is true and correct it if not. 
      * Output Feature Class: name this ParkBoundaries
      * Expression and Field Map can be left unchanged
      * Click Run
   4. After a moment of processing, the ParkBoundaries layer should appear in your TOC. Right-click and select 'Zoom to Layer' to ensure it appears as expected. Its datum should already be set to WGS84, but you can confirm this by examining the Source tab in the Layer Properties. 
6. Symbolize your feature layers with colors and symbols that are appropriate for the data that will be collected. 
7. In the 'Share' tab, click the 'Web Map' button to share your project as a web map, following these steps: 
   1. *Include your name in your map’s title, as I will need this information when I grade.*
   2. Add an appropriate summary and tags
   3. In the 'Select a Configuration' dropdown, select ''Editable"
   4. Share with University of Washington Tacoma Geospatial Tech and (using the Groups dropdown) with the MSGT 2022-2023 group
   5. Click 'Share'. It will take a moment for the process to complete. 

***Some important notes***

- For the ParkBoundaries feature class, we will import existing data rather than build the feature class from scratch. If you are not present in class to do this with the instructor, please view the relevant portion of the class recording before continuing. You can find a zip folder containing the shapefile we will use for the import on GitHub.
- You must define your domains before you create any feature classes that use those domains for drop-down options. This is counter-intuitive to many users, but it is important.
- Photos are not actually included as fields (i.e. not columns in an attribute table) but as attachments. See the class recording and linked instructions from ArcGIS.com for a reminder of how to enable attachments on a given feature class.
- Before publishing your service, make sure that your service is shared with this year's MSGT cohort on the 'Sharing' tab of the 'Service Editor' window.

### Part 3: Customize your map and prepare it for use

To ensure that you shared your map correctly, log in to your [AGOL account](https://www.arcgis.com/index.html) and examine your Content tab. At the top of the list of your AGOL content, you should see the map you created, along with corresponding Feature Layer and Service Definition. 

Click the button to 'Open in Map Viewer.' Use the Map Viewer interface to customize your basemap, documenting the customization you make to include in your lab write-up.  Use a basemap that is appropriate for your data collection scenario and purpose. Will your user likely collect data in bright light? Use a high contrast base map. Will your user need street-level detail or will a generalized basemap work? Would aerial imagery be useful to your user? Will the symbolization you created for your feature classes appear clearly on the basemap you selected? Make your choice based on these and other factors specific to your project. Save your changes. 

The next step to implementing a Field Maps instance is to build a map to customize the user’s experience of data collection further. Begin by visiting the [Field Maps web app](https://www.arcgis.com/apps/fieldmaps/) and sign in if you aren't already. You should see all the maps that are saved in your AGOL account, including the one you've just made. Click the map to open it. 

Here you can customize your Field Maps instance in a number of ways, including updating the form that will open when a user clicks an editable feature. Use [the documentation](https://doc.arcgis.com/en/field-maps/android/help/configure-the-form.htm) (topics 'Configure the form' through 'Configure the mobile app') to understand what is possible, and customize your map and app. Record the customization choices you make and justify your choices in your lab write-up. 

Once you have built your map and customized it appropriately, use the 'Sharing' tab to ensure it is shared with the cohort via the UWT ArcGIS organization. Again, *please include your name in your map’s title, as I will need this information when I grade and make sure it is shared with the MSGT 2022-2023 cohort.*

### Part 4: Test your app and collect sample data

In the Google Play Store or Apple App Store, download the ArcGIS Field Maps app on your mobile device. Log in using your UWT credentials, and navigate to your map. Download it, open it and test out data collection using your app.

First, collect some new data that doesn't currently exist in the database. You should collect at least one sample feature from each of your feature classes, except the ParkBoundaries layer. You may collect these sample data in the lab, at your home, or wherever else you are working from, or, if you are located near any Tacoma Metro Parks locations, you can collect them in the field. If you collect your samples from the lab/your home, still be sure to place your sample points inside the boundary of one of the existing parks. If you opt for field collection, please be safe and follow all the usual precautions. For the purposes of this lab, these do not need to be actually existing features, since, if you are collecting data from your home you may or may not know where a bathroom, path, etc. actually exists in a given park.  

For the ParkBoundaries layer, instead of collecting *new* data, update at least one existing feature. To do this, click on a Park to update, click the 'Edit' icon (looks like a pencil, should be in the bottom left corner of the screen), click the LastSurvey field and update the date the to current date and time. Click 'Submit' in the upper right corner without making any other changes.

Any edits you submit should be automatically uploaded. Log on to ArcGIS Online and view your map there to ensure that your edits were saved and submitted.

### Part 5: Brief reflection

Please provide responses to the following:

1. What is the name of your Field Map map instance? Please also paste into your write-up the link for sharing your map from the 'Sharing' settings of the Field Maps web app. 
2. Please describe the customization choices you made in designing the map you made for use in Collector. How did you choose to symbolize the layers and why? What base map, pop-up window, form, and application setting choices did you make, and why did you make them?
3. In the previous lab and this one, you used both ArcGIS Field Maps and Survey123 for ArcGIS. As the developer, describe your experience of working with these tools. How easy and difficult were the two systems to set up? What were their advantages and disadvantages? Repeat this process to compare the two systems from the perspective of the user/data collector. This should be a ~2 paragraph narrative to reflect on the process of both developing and using Collector and Survey123. I encourage you to use vocabulary about design and evaluation that we've used in class and in readings.

### Deliverables

- Your completed data schema tables from Part 1 (MS Word template available on GitHub)
- Reflection responses to the questions in Part 5, including the name of your Collector map, which I will access via the app and use to conduct my assessment.