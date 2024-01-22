# Supplementing Drupal migrations with feeds/files-based exports and imports #

## Purpose ##
Sometimes when migrating Drupal sites, there may be content fields that do not migrate, or are difficult to retrieve from the source database. If, however, most of the content is migrated, it is sometimes possible to export content fields from the source site and import into the target site using content feeds or exported data files. Although kludgy, compared to a standard Drupal migration, in a limited number of cases the feeds/files process can be faster and simpler.

## Limitations ##
This method is suitable for text or HTML field content. Feeds continues to have difficulties with associating paragraph and media content with nodes.

## Example use case ##

During the UA News migration, street addresses for events were created using the Drupal 7 Location module. Location fields are stored in a unique table in the Drupal 7 database, and can't be retrieved using regular entity reference or fieldset migrations. Therefore, the following steps:

### Preliminary migration steps \(kludgy\) ###
The following steps are a kludge but, as we will see, they are reversible kludge

1. Add a field called "prior node id" to your target content type on the destination site \-\-we will remove this field after the content import
- The syntax will look like the following:
    field_custom_prior_nid: nid

2. Run the migration import for the target content type
3. Spot check the imported content to make sure the prior node id field also populated.

In the case of the New Event migration, I also had to remove the original paragraph reference for event addresses from the content type and replace it with a regular fieldset. The Field Clone <https://www.drupal.org/project/fieldclone> module greatly sped up this process.


### Source Export ###

1. Install and enable \(or, just enable, if it's already installed\) the Views Data Export module <https://www.drupal.org/project/views_data_export> on the source site
2. Create a new view of the source content type and add a Data Export display \(In my case, I created a simple .CSV export, because I anticipated the data might require manual cleanup\)
3. In the view created above, add a row for the current node ID, and an individual row for each field that you intend to export
4. Sort the view by current node ID \(This will be used for manual validation/cleanup if needed\)
5. View the export feed or download the export data file and spot check to confirm the correct data is being emitted by the source site

