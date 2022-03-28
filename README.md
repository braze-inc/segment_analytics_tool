# Braze Segment Analytics Tool

This notebook will allow you to export a wide range of metrics for a single Segment from the Braze platform. Learn more about Segments in [Braze Docs](https://www.braze.com/docs/user_guide/engagement_tools/segments/)

## Why did we create the Segment Analytics Tool

- To empower our SMB customers the ability to access and engage with Segment analytics with minimal development or access to a full fledged Business Intelligence function
- To provide guidance to our customers on recommended methodologies for analyzing user engagement data
- Improve the implementation of recommended message engagement strategies, including [Cross-Channel Message Engagement](https://www.braze.com/resources/reports-and-guides/2021-global-customer-engagement-review) and [Campaign Tagging](https://www.braze.com/docs/user_guide/administrative/app_settings/manage_app_group/tags/)

- Enable self-service analytics for message engagement, retention, purchasing behavior, custom event engagement, and messaging opt-in rates as well as the impact of various strategies on each 

## Requirements

1. Install Python
2. Install Jupyter
3. Install packages listed under the **Packages** header in the script.
3. Fully onboard and integrate with the Braze SDK
4. Send messages via Campaigns
5. Create at least one Segment
6. Enable Segment Analytics Tracking for the Segment Created in step 5, or the Segment you are looking to analyze.
7. Fully tested integration between Braze and your instance of Amazon S3 via the AWS Secret Access Key method

Instructions for Amazon S3 integration can be viewed in [Braze Docs](https://www.braze.com/docs/partners/data_and_infrastructure_agility/cloud_storage/amazon_s3/).

If you have completed 1 - 6 successfully, you should have the following variable values available via the Braze Dashboard:

**app_group_api_key** - This is the string that allows you to access the API calls for an App Group. The App Group API Key can be found in the Braze Dashboard under Settings -> Developer Console -> Rest API Keys. The App Group API Key must have the following permissions granted in order for the tool to function properly:
- `users.export.segment`
- `segments.list`
- `campaigns.details`

**API_URL** - This is the URL used to make the Rest API call

**SEGMENT_ID** - This is the Segment API Identifier used to return user data from the segment for the API call. This script can only return results for one segment at a time, and it is recommmended that the segment have no more than 200k users due to hardware limitations that were verified during testing. The Segment API Identifier can be found in the Braze Dashboard under Settings -> Developer Console -> Additional API Identifiers. Under the dropdown menu select 'Segments' and then click the 'Search for a value' dropdown to see a list of segments. Select the segement name that you wish to return results for and copy the value listed under "API Identifier".

**access_key** - Listed under "AWS Access ID"

**secret_key** - Listed under "AWS Secret Access Key"

**region_name** - The region that your S3 bucket is listed under

**user_export_bucket_name** - The name of the S3 storage bucket that you would like to store the User Profile Export in. 

***The above variables should never be shared outside of your organization, or be saved in a publically accessible workspace.***


## What does the Segment Analytics Tool do?

- The Segment Analytics Tool will make a series of requests to the following API Enpoints:

- [Segment List API](https://www.braze.com/docs/api/endpoints/export/segments/get_segment/)
- [Users by Segment API](https://www.braze.com/docs/api/endpoints/export/user_data/post_users_segment/)
- [Campaign Details API](https://www.braze.com/docs/api/endpoints/export/campaigns/get_campaign_details/)

    *A successful request should result in several .zip files being stored in a folder in the S3 Bucket that is denoted by the day the request was run for the Users by Segment API.*

- The zip files stored in S3 are exported from S3 to the notebook.
- The zip files are processed and transformed into a pandas dataframe.
- Various fields are extracted from the dataframe columns, and some new columns are created from the extracted columns.
- The response from the Campaign Details API is stored as a dataframe.
- The two dataframes are combined to create several others to produce analytical outputs, which are described below.
- The script outputs a `.xlsx` file containing all of the analytical outputs that were run in the notebook.

## Instructions

1. Complete 1 - 6 listed under the **Requirements** header
2. Input all of the required variables, which are detailed in the notebook.
3. Run All

*The script may fail if say, you aren't tracking purchases or custom events, but those sections can simply be skipped if you are not tracking particular elements of your application with Braze.*

## Summary of Analytical Outputs

**Message Engagement**

The following Message Engagement Rates will be returned:

- Number of Users
- Email Open Rate
- Email Click Rate
- Push Open Rate
- In-App Message Click Rate
- Message Conversion Rates (of all Conversion Criteria)
- Content Card Click Rate

**Rolling Retention**

- Rolling Retention Rates

*You can view the Rolling Retention Methodology [here](https://www.braze.com/resources/articles/calculate-retention-rate).*

**Purchasing Stats** 

The following purchasing metrics will be returned in the first purchasing stats dataframe:

- Number of Buyers
- Number of Repeat Buyers
- % Buyers
- % Repeat Buyers
- Number of Purchases
- Total Revenue
- Average Revenue per Buyer
- Average time to Purchase
- Purchases per Buyer

The second purchasing stats dataframe will return purchase retention rates, also using a rolling retention methodology from the date of first purchase.

**Session Stats**

- Number of Users
- Sessions per User

**Custom Events Stats**

The following Custom Events Stats will be returned:

- Number of Users Completing the Custom Event
- Number of Users
- Total Count of Custom Event
- % of Users Completing Custom Events
- Custom Events per User
- Avg. Days between each occurence of a Custom Event
- Avg. Custom Event Completion per Day

**Custom Events Used in Campaigns**

- A list of all custom events that used in campaigns received by the selected segment.

**Segment Messaging Opt-In Rates**

Returns Push and Email Opt-In Rates for all users across the following platforms:

- iOS
- Android
- Web

**A brief note on the methodology**

Many of the analytical outputs are also crossed with the following dimensions from Message Engagement in addition to the Segment level:

- Channel

- Channel Combo

- Campaign Tag

These dimensions are parsed at the Campaign level, and are then left-joined to the User level engagement data. Because this information was not parsed at the user-level, this results in fan-out, as users more often than not receive multiple campaigns over a given period of time. For many of the "total" metrics, the correct value is always the one at the segment-level. We felt it was still valuable to include this, as most of the metrics are rates, or percentages which calculate correctly.

*Estimated Execution Times *

For a segment that was exported on a prior date:


| # of User Profiles | Exec. Time |
| ---------          | ---------- |
| 10k                | 5 min      |
| 50k                | 15 min     |
| 100k               | 25 min     |
| 200k               | 45 min     |

n.b. that these estimates were gathered using a fully spec'd MacBook pro. Your execution times will vary.

If executing and analyzing on the same day, add 5 minutes to each estimate.

Contributions Welcome
