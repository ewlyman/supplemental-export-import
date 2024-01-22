# Supplementing Drupal migrations with feeds-based exports and imports #

## Purpose ##
Sometimes when migrating Drupal sites, there may be content that does not migrate, or is difficult to retrieve from the source database. If, however, most of the content is migrated, it is sometimes possible to export content from the source site and import into the target site using content feeds. Although kludgy, compared to a standard Drupal migration, in a limited number of cases the feeds process can be faster and simpler.

## Limitations ##
This method is suitable for text or HTML field content. Feeds continues to have difficulties with associating paragraph and media content with nodes.

## Example use case ##
