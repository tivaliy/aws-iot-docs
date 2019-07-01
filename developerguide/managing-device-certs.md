# X\.509 Certificates and AWS IoT<a name="managing-device-certs"></a>

Your devices can use X\.509 certificates to authenticate with AWS IoT\.

## Client Authentication<a name="client-authentication"></a>

 You can use a self\-signed certificate or AWS IoT can generate one for you\. To have AWS IoT generate a certificate for you, use the AWS IoT console, create\-keys\-and\-certificate CLI command, or the `CreateKeysAndCertificate` API \. Certificates generated by AWS IoT are long\-lived, but expire at 2049\-12\-31T23:59:59Z \(that is, at midnight GMT on December 31, 2049\)\.

To create a self\-signed certificate, use OpenSSL or similar toolset\. You can also register a CA certificate, sign your self\-signed certificate, and then register the self\-signed certificate with AWS IoT\.

To register a CA certificate with AWS IoT, use the register\-ca\-certificate CLI command or the `RegisterCaCertificate` API\. Sign your self\-signed certificate using the registered CA\. You can then use the register\-certificate CLI command or `RegisterCertificate` API to register any self\-signed certificate signed with the registered CA with AWS IoT\.

**Note**  
Devices must support rotation and replacement of certificates to ensure smooth operation as certificates expire\.

You can use the AWS IoT console or CLI to perform the following certificate operations:
+ Create and register an AWS IoT certificate\.
+ Register a CA certificate\.
+ Register a device certificate\.
+ Activate or deactivate a device certificate\.
+ Revoke a device certificate\.
+ Transfer a device certificate to another AWS account\.
+ List all CA certificates registered to your AWS account\.
+ List all device certificates registered to your AWS account\.

For more information about the CLI commands to perform these operations, see [AWS IoT CLI Reference](https://docs.aws.amazon.com/cli/latest/reference/iot/index.html)\.

For more information about using the AWS IoT console to create certificates, see [Create and Activate a Device Certificate](https://docs.aws.amazon.com/iot/latest/developerguide/create-device-certificate.html)\.

## Server Authentication<a name="server-authentication"></a>

Server certificates allow your devices to verify that they're communicating with AWS IoT and not another server impersonating AWS IoT\. Service certificates must be copied onto your device and referenced when devices connect to AWS IoT\. For more information, see the [AWS IoT Device SDKs](iot-sdks.md)\. 

AWS IoT server certificates are signed by one of the following CA certificates:

**VeriSign Endpoints \(legacy\)**
+ RSA 2048 bit key: [VeriSign Class 3 Public Primary G5 root CA certificate](https://www.symantec.com/content/en/us/enterprise/verisign/roots/VeriSign-Class 3-Public-Primary-Certification-Authority-G5.pem)

**Amazon Trust Services Endpoints \(preferred\)**
+ RSA 2048 bit key: [Amazon Root CA 1](https://www.amazontrust.com/repository/AmazonRootCA1.pem)\.
+ RSA 4096 bit key: Amazon Root CA 2 \- Reserved for future use\.
+ ECC 256 bit key: [Amazon Root CA 3](https://www.amazontrust.com/repository/AmazonRootCA3.pem)\.
+ ECC 384 bit key: Amazon Root CA 4 \- Reserved for future use\.

We recommend that all customers create an Amazon Trust Services \(ATS\) endpoint and load these CA certificates onto their devices to avoid any issues with the widespread distrust by browsers of Symantec CAs \(including VeriSign\) that occurred in October 2018\. For backward compatibility, we still support customers who use these endpoints\. Customers can retreive their ATS endpoint by calling the `describe-endpoint` API with the `iot:Data-ATS` endpoint type\. Devices operating on ATS endpoints are fully interoperable with devices operating on Symantec endpoints in the same account\. They do not need to be reregistered\.

aws iot describe\-endpoint \-\-endpoint\-type iot:Data\-ATS

Storing all of these certificates on your device can take up valuable memory space\. If your devices implement RSA\-based validation, you can omit the [Amazon Root CA 3](https://www.amazontrust.com/repository/AmazonRootCA3.pem) and [Amazon Root CA 4](https://www.amazontrust.com/repository/AmazonRootCA4.pem) ECC certificates\. If your devices implement ECC\-based certificate validation, you can omit the [Amazon Root CA 1](https://www.amazontrust.com/repository/AmazonRootCA1.pem) and [Amazon Root CA 2](https://www.amazontrust.com/repository/AmazonRootCA2.pem) RSA certificates\. 

All new AWS IoT Core regions, starting with the May 9, 2018 launch of AWS IoT Core in the Asia Pacific \(Mumbai\) Region, serve ATS certificates only\.

**Note**  
CA certificates have an expiration date after which they cannot be used to validate a server's certificate\. CA certificates might have to be replaced before their expiration date\. Make sure that you can update the root CA certificates on all of your devices to ensure ongoing connectivity and to keep up\-to\-date with security best practices\.

For a complete list of CA certificates used by AWS IoT, see [Amazon Trust Services](https://www.amazontrust.com/repository/)\.