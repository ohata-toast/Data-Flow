## Data & Analytics > DataFlow > Tutorial

### Create Flow 

![chapter1.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter1_2025_08.png)

① Click **Create Flow** button.
② Enter **Flow Name**.
③ Enter **Flow Description**.
④ Select **Execution Mode**.
   - **STREAMING**: it is set automatically by selecting a flow template.
⑤ Select **LNCS to OBS** from **Flow Template**.
   - **LNCS to OBS** template is a flow that searches and converts data from `NHN Cloud Log & Crash Search` and saves it, being stored on `Object Storage`.
⑥ Select **Instance Flavor**.

### Log & Crash Search Node Definition 

![chapter2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2_2025_08.png)

① Click **Flow Information** tab of the generated flow.
② Click **(NHN Cloud) Log&Crash Search** node.
③ Enter **Appkey** and **Secretkey** of (NHN Cloud) Log&Crash Search to be designated as the data source.

### filter success response Node Definition

![chapter2-2.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter2-2_2025_08.png)

① Click **filter success response** node.
② In the **LNCS to OBS** template, a conditional statement is written to pass the IF node only if the data query result of the Log&Crash Search Source node is normal.
> If changing 'True' to 'False'. The IF node will be passed unless the data query result of the Log&Crash Search Source is normal.

### Cipher Node Definition

![chapter3.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter3_2025_08.png)

① Click **Cipher** node.
② Enter the information below to use SKM:
 - **Key Version**: The symmetric key version of the Security Key Manager (SKM) key store to use
 - **Appkey**: the appkey of SKM
 - **Key ID**: The symmetric key ID of the SKM key store 

!!! tip "Tip"
    The symmetric key version can be found in the Secure Key Manager web console under Key Details.

### Define Object Storage Node and Save Flow 

![chapter4.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter4_2025_08.png)

① Click **(NHN Cloud) Object Storage** node.
② Enter the bucket name to store data.
③ Enter the credential information to access the bucket.
  - **Access Key**: S3 API credential access key
  - **Secret Key**: S3 API credential secret key
④ Save the flow by clicking **Save Flow** button.

!!! tip "Tip"
    S3 API credentials access key and secret key can be issued through the Object Storage web console or through Object Storage's S3 API credential issuance API.

### Execute Flow

![chapter5.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter5_2025_08.png)

① Select the flow to execute.
② Execute the flow by clicking the View More icon > **Start Flow** button.

### Job After Execution

![chapter6.png](http://static.toastoven.net/prod_dataflow/ko/tutorial/chapter6_2025_08.png)

① Once you start the flow, the execution status will change to green after 1-2 minutes.
② You can click the **View Log** button to check detailed logs for flow execution.