# Overview #

Amazon supports PHP, C#, and Java interfaces to MWS, and users have developed [Ruby](https://github.com/dmichael/amazon-mws) and [Perl](https://github.com/frodwith/Amazon-MWS) versions. But strangely a Python version does not exist so I created this module to cover the basic functionality I needed for a project.

API is [documented here](https://developer.amazonservices.com/)


# Example #

```
import csv
from StringIO import StringIO
from mws import MWS

def get_report(access_key, secret_key, merchant_id, marketplace_id, report_type='_GET_FLAT_FILE_OPEN_LISTINGS_DATA_'):
    """Get this report type and return details in list
    """
    mws = MWS(access_key=access_key, secret_key=secret_key, merchant_id=merchant_id, marketplace_id=marketplace_id)    
    mws.request_report(report_type) # generate new version of report so have updated data next time
    reports = mws.get_report_request_list()
    for report in reports.GetReportRequestListResponse.GetReportRequestListResult.ReportRequestInfo:
        if report.ReportProcessingStatus == '_DONE_' and report.ReportType == report_type:
            # extract contents of report
            products = {}
            sio = StringIO(mws.get_report(report.GeneratedReportId))
            header = None
            for sku, asin, price, quantity in csv.reader(sio, delimiter='\t'):
                if header:
                    products[asin] = dict(SKU=sku, Price=price, Quantity=quantity, ASIN=asin)                 
                else:
                    header = True
            return products
    return [] # unfortunately report does not exist (yet)
```


# Contact #

Let me know if you want to extend or maintain this module:
richard@[webscraping.com](http://webscraping.com)