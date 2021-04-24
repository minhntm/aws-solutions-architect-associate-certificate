# Snowball

Snowballs are secure hardware devices that serves to collect data in austere environments or environments not connected to the internet and transfer this to the AWS cloud. This can be done by sending the devices back and forth between the locations and AWS by mail. Snowball is suited for petabyte-scale data transfer.

# Storage gateway

- File Gateway
    For flat files, stored directly on S3

    ![File Gateway](./images/file-gateway.png)

- Volume Gateway
    - Stored Volumes - Entire Dataset is stored on site and is asynchronously backed up to S3

    ![Stored Volumes](./images/stored-volumes.png)

    - Cached Volumes - Entire Dataset is stored on S3 and the most frequently accessed data is cached on site

    ![Cached Volumes](./images/cached-volumes.png)

- Gateway Virtual Tape Library

![Gateway Virtual Tape Lib](./images/gateway-virtual-tape.png)


### Tips:

- Low-latency service of the majority (entire dataset) of files is required ⇒ gateway stored + file gateways
- Low-latency service of the last few days of files is required ⇒ gateway-cached + file gateways
- Cost-effectively and durably archive backup data in GLACIER or DEEP_ARCHIVE ⇒ VTL