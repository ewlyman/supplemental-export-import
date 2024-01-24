# Supplementing Drupal migrations with feeds/files-based exports and imports #

## Purpose ##
Sometimes when migrating Drupal sites, there may be content fields that do not migrate, or are difficult to retrieve from the source database. If, however, most of the content is migrated, it is sometimes possible to export content fields from the source site and import into the target site using content feeds or exported data files. Although kludgy, compared to a standard Drupal migration, in a limited number of cases the feeds/files process can be faster and simpler.

## Limitations ##
This method is suitable for text or HTML field content. Feeds continues to have difficulties with associating paragraph and media content with nodes.

## Example use case ##

During the UA News migration, street addresses for events were created using the Drupal 7 Location module. Location fields are stored in a unique table in the Drupal 7 database, and can't be retrieved using regular entity reference or fieldset migrations. Therefore, the following steps:

### Preliminary migration steps \(kludgy\) ###
The following steps are a kludge but, as we will see, they are a necessary and reversible kludge

1. Add a field called "prior node id" to your target content type on the destination site \-\-we will remove this field after the content import
- The syntax will look like the following:
    *field_custom_prior_nid: nid*

2. Run the migration import for the target content type
3. Spot check the imported content to make sure the prior node id field also populated.
4. Refer to the file *news_event_location_export_feed.txt* for a copy of the export view used on the UA News site. The file *event\-location\-export.csv* is an example of feed output.

In the case of the News Event migration, I also had to remove the original paragraph reference for event addresses from the content type and replace it with a regular fieldset. The Field Clone <https://www.drupal.org/project/fieldclone> module greatly sped up this process.


### Source Export Setup and Download ###

1. Install and enable \(or, just enable, if it's already installed\) the Views Data Export module <https://www.drupal.org/project/views_data_export> on the source site
2. Create a new view of the source content type and add a Data Export display \(In my case, I created a simple \.CSV export, because I anticipated the data might require manual cleanup\)
3. In the view created above, add a row for the current node ID, and additional individual rows for each field that you intend to export
4. Sort the view by current node ID \(This will be used for manual validation/cleanup if needed\)
5. View the export feed or download the export data file and spot check to confirm the correct data is being emitted by the source site

### Target Import Setup ###
1. Install and enable \(or, again, just enable) the Feeds module <https://www.drupal.org/project/feeds> on the target site
2. Create a new Feed type at *admin \> structure \>feeds* Good documentation for how to build feed parsers is available at <https://www.drupal.org/docs/contributed-modules/feeds/creating-and-editing-import-feeds> 
- In the specific use case for News Event street addresses, the following settings were used
    - Import period *Off* \(Because we were uploading a single CSV file\)
    - No changes to *Fetcher Settings*
    - No changes to *Parser Settings*
    - Processor settings were: do not insert new, update previous items, keep previous items, never expire items and under *Advanced settings* create a revision and force update. \(refer to included png *Screenshot Event Adress Feed Mappings*\)
3. To set up feed mappings, please follow the instructions at <https://www.drupal.org/docs/contributed-modules/feeds/creating-and-editing-import-feeds#mapping-sources-targets>
- I used the source\_nid/Prior NID as the unique field, as this would match street addresses from Event nodes on the source site to street addresses for the corresponding Event nodes on the target site.
- Please refer to the included png *Screenshot Event Addresses Feed Mappings* for an example \(these are the field definitions I used\)

###  Target Import Process ###
1. Create the Feed importer at *admin \> content \> feeds* \-if you have more than one Feed type on the site, when you select *Add feed* you will need to select the feed type you created in the previous step
2. You will be prompted to add a title for the feed and upload the import \.CSV file
3. Uncheck *Active* in the *Import options* tab, and then select the *Save and Import* button
- Refer to the png files *Screenshot Event add feed* and *Screenshot Event finished feed*. 
- *Screenshot import complete* shows the status message that appears when the import is finished.
4. Spot check the target content type to confirm the Feeds import.





