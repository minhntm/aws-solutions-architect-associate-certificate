# Snowball

Snowball is a petabyte-scale data transport solution that uses secure appliances to transfer large amounts of data into and out of AWS.

# Storage gateway

- File Gateway
    For flat files, stored directly on S

    ![File Gateway](./images/file-gateway.png)

- Volume Gateway
    - Stored Volumes - Entire Dataset is stored on site and is asynchronously backed up to S3

    ![Stored Volumes](./images/stored-volumes.png)

    - Cached Volumes - Entire Dataset is stored on S3 and the most frequently accessed data is cached on site

    ![Cached Volumes](./images/cached-volumes.png)

- Gateway Virtual Tape Library

![Gateway Virtual Tape Lib](./images/gateway-virtual-tape.png)


### Tips:

- low-latency service of the majority (entire dataset) of files is required ⇒ gateway stored + file gateways
- low-latency service of the last few days of files is required ⇒ gateway-cached + file gateways
- cost-effectively and durably archive backup data in GLACIER or DEEP_ARCHIVE ⇒ VTL