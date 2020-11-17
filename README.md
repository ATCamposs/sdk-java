# Stark Bank Java SDK

Welcome to the Stark Bank Java SDK! This tool is made for Java 
developers who want to easily integrate with our API.
This SDK version is compatible with the Stark Bank API v2.

If you have no idea what Stark Bank is, check out our [website](https://starkbank.com/) 
and discover a world where receiving or making payments 
is as easy as sending a text message to your client!

## Help and Feedback

If you have any questions about our SDK, just email us your questions. 
We will respond you quickly, pinky promise. We are here to help you integrate with us ASAP. 
We also love feedback, so don't be shy about sharing your thoughts with us.

Email: developers@starkbank.com

## Supported Java Versions

This library supports the following Java versions:

* Java 8+

If you have specific version demands for your projects, feel free to contact us.

## Stark Bank API Reference

If you want to take a look at our API, follow [this link](https://starkbank.com/docs/api).

## Versioning

This project adheres to the following versioning pattern:

Given a version number MAJOR.MINOR.PATCH, increment:

- MAJOR version when the **API** version is incremented. This may include backwards incompatible changes;
- MINOR version when **breaking changes** are introduced OR **new functionalities** are added in a backwards compatible manner;
- PATCH version when backwards compatible bug **fixes** are implemented.

## Setup

### 1. Install our SDK

Manually download the desired SDK version with the JAR found in our
[GitHub page](https://github.com/starkbank/sdk-java/releases/latest)
and add it to your project.

1.1 If you are using Gradle, add this line to your dependencies in build.gradle:

```sh
dependencies {
  implementation 'com.starkbank:sdk:2.1.0'
}
```

1.2 If you are using Maven, add this dependency to your pom.xml:

```xml
<dependency>
  <groupId>com.starkbank</groupId>
  <artifactId>sdk</artifactId>
  <version>2.1.0</version>
</dependency>
```

**Note**: If you are using Android, don't forget to [add the compileOptions to your build.gradle](https://developer.android.com/studio/write/java8-support):

```gradle
compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
}
```

### 2. Create your Private and Public Keys

We use ECDSA. That means you need to generate a secp256k1 private
key to sign your requests to our API, and register your public key
with us so we can validate those requests.

You can use one of following methods:

2.1. Check out the options in our [tutorial](https://starkbank.com/faq/how-to-create-ecdsa-keys).

2.2. Use our SDK:

```java
import com.starkbank.*;

Key key = Key.create();

String privatePem = key.privatePem;
String publicPem = key.publicPem;

System.out.print(privatePem);
System.out.print(publicPem);

// or, to also save .pem files in a specific path
Key key = Key.create("file/keys/");

String privatePem = key.privatePem;
String publicPem = key.publicPem;

System.out.print(privatePem);
System.out.print(publicPem);
```

### 3. Create a Project

You need a project for direct API integrations. To create one in Sandbox:

3.1. Log into [Starkbank Sandbox](https://sandbox.web.starkbank.com)

3.2. Go to Menu > Usuários (Users) > Projetos (Projects)

3.3. Create a Project: Give it a name and upload the public key you created in section 2.

3.4. After creating the Project, get its Project ID

3.5. Use the Project ID and private key to create the object below:

```java
import com.starkbank.*;

// Get your private key from an environment variable or an encrypted database.
// This is only an example of a private key content. You should use your own key.
String privateKeyContent = "-----BEGIN EC PARAMETERS-----\nBgUrgQQACg==\n-----END EC PARAMETERS-----\n-----BEGIN EC PRIVATE KEY-----\nMHQCAQEEIMCwW74H6egQkTiz87WDvLNm7fK/cA+ctA2vg/bbHx3woAcGBSuBBAAK\noUQDQgAE0iaeEHEgr3oTbCfh8U2L+r7zoaeOX964xaAnND5jATGpD/tHec6Oe9U1\nIF16ZoTVt1FzZ8WkYQ3XomRD4HS13A==\n-----END EC PRIVATE KEY-----";

Project project = new Project(
    "sandbox",
    "5656565656565656",
    privateKeyContent
);
```

NOTE 1: Never hard-code your private key. Get it from an environment variable or an encrypted database.

NOTE 2: We support `"sandbox"` and `"production"` as environments.

NOTE 3: The project you created in `sandbox` does not exist in `production` and vice versa.


### 4. Setting up the user

There are two kinds of users that can access our API: **Project** and **Member**.

- `Member` is the one you use when you log into our webpage with your e-mail.
- `Project` is designed for integrations and is the one meant for our SDK.

There are two ways to inform the user to the SDK:
 
4.1 Passing the user as argument in all functions:

```java
import com.starkbank.*;

Balance balance = Balance.get(project);
```

4.2 Set it as a default user in the SDK:

```java
import com.starkbank.*;

Settings.user = project;

Balance balance = Balance.get();
```

Just select the way of passing the project user that is more convenient to you.
On all following examples we will assume a default user has been set.

### 5. Setting up the error language

The error language can also be set in the same way as the default user:

```java
import com.starkbank.*;

Settings.language = "en-US";
```

Language options are "en-US" for english and "pt-BR" for brazilian portuguese. English is default.


## Testing in Sandbox

Your initial balance is zero. For many operations in Stark Bank, you'll need funds
in your account, which can be added to your balance by creating a Boleto. 

In the Sandbox environment, 90% of the created Boletos will be automatically paid,
so there's nothing else you need to do to add funds to your account. Just create
a few and wait around a bit.

In Production, you (or one of your clients) will need to actually pay this Boleto
for the value to be credited to your account.


## Usage

Here are a few examples on how to use the SDK. If you have any doubts, use the built-in
`help()` function to get more info on the desired functionality
(for example: `help(starkbank.boleto.create)`)

### Get balance

To know how much money you have in your workspace, run:

```java
import com.starkbank.*;

Balance balance = Balance.get();

System.out.println(balance);
```

### Get dict key

You can get PIX key's parameters by its id.

```java
import com.starkbank.*;

DictKey dictKey = DictKey.get("tony@starkbank.com");

System.out.println(dictKey);
```

### Create invoices

You can create dynamic QR Code invoices to charge customers or to receive money from accounts
you have in other banks.

```java
import com.starkbank.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

List<Invoice> invoices = new ArrayList<>();
HashMap<String, Object> data = new HashMap<>();
data.put("amount", 400000);
data.put("due", "2020-11-28T17:59:26.249976+00:00");
data.put("taxId", "20.018.183/0001-80");
data.put("name", "Iron Bank S.A.");
data.put("expiration", 123456789);
data.put("fine", 2);
data.put("interest", 1.3);

List<HashMap<String, Object>> descriptions = new ArrayList<>();
HashMap<String, Object> description = new HashMap<>();
description.put("key", "Some supplies");
description.put("value", "100000");
descriptions.add(description);
data.put("descriptions", descriptions);

List<HashMap<String, Object>> discounts = new ArrayList<>();
HashMap<String, Object> discount = new HashMap<>();
discount.put("due", getDatetimeString(1));
discount.put("percentage", 2.5);
data.put("discounts", discounts);

invoices.add(new Invoice(data));

for (Invoice invoice : invoices) {
    System.out.println(invoice);
}
```

**Note**: Instead of using Invoice objects, you can also pass each invoice element in map format

### Get an invoice

After its creation, information on an invoice may be retrieved by its id. 
Its status indicates whether it's been paid.

```java
import com.starkbank.*;

Invoice invoice = Invoice.get("5155165527080960")

System.out.println(invoice);
```

### Get an invoice QR Code

After its creation, an invoice QR Code png file may be retrieved by its id. 

```java
import java.io.File;
import java.io.InputStream;
import java.nio.file.StandardCopyOption;
import com.starkbank.*;

HashMap<String, Object> options = new HashMap<>();
InputStream png = Invoice.qrcode("5155165527080960");

java.nio.file.Files.copy(
    png,
    new File("qrcode.png").toPath(),
    StandardCopyOption.REPLACE_EXISTING
);
```

### Get an invoice PDF

After its creation, an invoice PDF may be retrieved by its id. 

```java
import java.io.File;
import java.io.InputStream;
import java.nio.file.StandardCopyOption;
import com.starkbank.*;

HashMap<String, Object> options = new HashMap<>();
options.put("layout", "booklet");
InputStream pdf = Invoice.pdf("5155165527080960", options);

java.nio.file.Files.copy(
    pdf,
    new File("invoice.pdf").toPath(),
    StandardCopyOption.REPLACE_EXISTING
);
```

Be careful not to accidentally enforce any encoding on the raw pdf content,
as it may yield abnormal results in the final file, such as missing images
and strange characters.

### Cancel an invoice

You can also cancel an invoice by its id.
Note that this is not possible if it has been paid already.

```java
import com.starkbank.*;

HashMap<String, Object> patchData = new HashMap<>();
patchData.put("status", "canceled");
Invoice invoice = Invoice.update("5155165527080960", patchData);

System.out.println(invoice);
```

### Update an invoice

You can update an invoice's amount, due date and expiration by its id.
Note that this is not possible if it has been paid already.

```java
import com.starkbank.*;

HashMap<String, Object> patchData = new HashMap<>();
patchData.put("status", "canceled");
patchData.put("amount", 999999);
patchData.put("due", "2020-11-02T23:06:42.924000+00:00");
patchData.put("expiration", 123456789);
Invoice invoice = Invoice.update("5155165527080960", patchData);

System.out.println(invoice);
```

### Query invoices

You can get a list of created invoices given some filters.

```java
import com.starkbank.*;

HashMap<String, Object> params = new HashMap<>();
params.put("status", "created");
params.put("limit", 1);
params.put("after", "2019-04-01");
params.put("before", "2030-04-30");

Generator<Invoice> invoices = Invoice.query(params);
for (Invoice invoice : invoices) {
    System.out.println(invoice);
}
```

### Query invoice logs

Logs are pretty important to understand the life cycle of an invoice.

```java
import com.starkbank.*;

HashMap<String, Object> params = new HashMap<>();
params.put("limit", 3);
params.put("after", "2019-04-01");
params.put("before", "2030-04-30");
Generator<Invoice.Log> logs = Invoice.Log.query(params);

for (Invoice.Log log : logs) {
    System.out.println(log);
}
```

### Get an invoice log

You can get a single log by its id.

```java
import com.starkbank.*;

Invoice.Log log = Invoice.Log.get("5155165527080960");

System.out.println(log);
```

### Query deposits

You can get a list of created deposits given some filters.

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<Deposit> deposits = Deposit.query(params);

for (Deposit deposit : deposits){
    System.out.println(deposit);
}
```

### Get a deposit

After its creation, information on a deposit may be retrieved by its id. 

```java
import com.starkbank.*;

Deposit deposit = Deposit.get("5730174175805440");

System.out.println(deposit);
```

### Query deposit logs

Logs are pretty important to understand the life cycle of a deposit.

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<Deposit.Log> logs = Deposit.Log.query(params);

for (Deposit.Log log : logs){
    System.out.println(log);
}
```

### Get a deposit log

You can get a single log by its id.

```java
import com.starkbank.*;

Deposit.Log log = Deposit.Log.get("6532638269505536");

System.out.println(log);
```


### Create boletos

You can create boletos to charge customers or to receive money from accounts
you have in other banks.

```java
import com.starkbank.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

List<Boleto> boletos = new ArrayList<>();
HashMap<String, Object> data = new HashMap<>();
data.put("amount", 400000);
data.put("name", "Iron Bank S.A.");
data.put("taxId", "20.018.183/0001-80");
data.put("streetLine1", "Av. Faria Lima, 1844");
data.put("streetLine2", "CJ 13");
data.put("district", "Itaim Bibi");
data.put("city", "São Paulo");
data.put("stateCode", "SP");
data.put("zipCode", "01500-000");
data.put("due", "2021-05-20");
data.put("fine", 2.5);
data.put("interest", 1.3);
data.put("overdueLimit", 5);
data.put("tags", new String[]{"War supply", "Invoice #1234"});

List<Boleto.Description> descriptions = new ArrayList<>();
descriptions.add(new Boleto.Description("taxes", 3000));
descriptions.add(new Boleto.Description("this will be an incredible payment"));
data.put("descriptions", descriptions);

List<Boleto.Discount> discounts = new ArrayList<>();
discounts.add(new Boleto.Discount("2020-05-17", 2.5));
discounts.add(new Boleto.Discount("2020-05-18", 2.0));
data.put("discounts", discounts);

boletos.add(new Boleto(data));

boletos = Boleto.create(boletos);

for (Boleto boleto : boletos){
    System.out.println(boleto);
}
```

**Note**: Instead of using Boleto objects, you can also pass each boleto element in HashMap format

### Query boletos

You can get a list of created boletos given some filters.

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<Boleto> boletos = Boleto.query(params);

for (Boleto boleto : boletos){
    System.out.println(boleto);
}
```

### Get boleto

After its creation, information on a boleto may be retrieved by passing its id. 
Its status indicates whether it's been paid.

```java
import com.starkbank.*;

Boleto boleto = Boleto.get("5730174175805440");

System.out.println(boleto);
```

### Get boleto PDF

After its creation, a boleto PDF may be retrieved by passing its id. 

```java
import java.io.File;
import java.io.InputStream;
import java.nio.file.StandardCopyOption;
import com.starkbank.*;

HashMap<String, Object> options = new HashMap<>();
options.put("layout", "booklet");

InputStream pdf = Boleto.pdf("5915632394567680", options);

java.nio.file.Files.copy(
    pdf,
    new File("boleto.pdf").toPath(),
    StandardCopyOption.REPLACE_EXISTING
);
```

Be careful not to accidentally enforce any encoding on the raw pdf content,
as it may yield abnormal results in the final file, such as missing images
and strange characters.

### Delete boleto

You can also cancel a boleto by its id.
Note that this is not possible if it has been processed already.

```java
import com.starkbank.*;

Boleto boleto = Boleto.delete("5669456873259008");

System.out.println(boleto);
```

### Query boleto logs

Logs are pretty important to understand the life cycle of a boleto.

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<Boleto.Log> logs = Boleto.Log.query(params);

for (Boleto.Log log : logs){
    System.out.println(log);
}
```

### Get a boleto log

You can get a single log by its id.

```java
import com.starkbank.*;

Boleto.Log log = Boleto.Log.get("6532638269505536");

System.out.println(log);
```

### Create transfers

You can also create transfers in the SDK (TED/PIX).

```java
import com.starkbank.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

List<Transfer> transfers = new ArrayList<>();
HashMap<String, Object> data1 = new HashMap<>();
data1.put("amount", 100000000);
data1.put("bankCode", "341"); # TED
data1.put("branchCode", "2201");
data1.put("accountNumber", "76543-8");
data1.put("taxId", "594.739.480-42");
data1.put("name", "Daenerys Targaryen Stormborn");
data1.put("scheduled", "2020-12-11");
data1.put("tags", new String[]{"daenerys", "invoice/1234"});
transfers.add(new Transfer(data1));

HashMap<String, Object> data2 = new HashMap<>();
data2.put("amount", 100000000);
data2.put("bankCode", "20018183"); # PIX
data2.put("branchCode", "2201");
data2.put("accountNumber", "76543-8");
data2.put("taxId", "594.739.480-42");
data2.put("name", "Daenerys Targaryen Stormborn");
data2.put("scheduled", "2020-11-11T15:01:39.903667+00:00");
data2.put("tags", new String[]{"daenerys", "invoice/1234"});
transfers.add(new Transfer(data2));

transfers = Transfer.create(transfers);

for (Transfer transfer : transfers){
    System.out.println(transfer);
}
```

**Note**: Instead of using Transfer objects, you can also pass each transfer element in HashMap format

### Query transfers

You can query multiple transfers according to filters.

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<Transfer> transfers = Transfer.query(params);

for (Transfer transfer : transfers){
    System.out.println(transfer);
}
```

### Get transfer

To get a single transfer by its id, run:

```java
import com.starkbank.*;

Transfer transfer = Transfer.get("6532638269505536");

System.out.println(transfer);
```

### Cancel a scheduled transfer

To cancel a scheduled transfer by its id, run:

```java
import com.starkbank.*;

Transfer transfer = Transfer.delete("6532638269505536");

System.out.println(transfer);
```

### Get transfer PDF

After its creation, a transfer PDF may also be retrieved by passing its id. 

```java
import java.io.File;
import java.io.InputStream;
import java.nio.file.StandardCopyOption;
import com.starkbank.*;

InputStream pdf = Transfer.pdf("6266195376340992");

java.nio.file.Files.copy(
    pdf,
    new File("transfer.pdf").toPath(),
    StandardCopyOption.REPLACE_EXISTING
);
```

Be careful not to accidentally enforce any encoding on the raw pdf content,
as it may yield abnormal results in the final file, such as missing images
and strange characters.

### Query transfer logs

You can query transfer logs to better understand transfer life cycles.

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<Transfer.Log> logs = Transfer.Log.query(params);

for (Transfer.Log log : logs){
    System.out.println(log);
}
```

### Get a transfer log

You can also get a specific log by its id.

```java
import com.starkbank.*;

Transfer.Log log = Transfer.Log.get("6532638269505536");

System.out.println(log);
```

### Pay a BR Code

Paying a BR Code is also simple.

```java
import com.starkbank.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

List<BrcodePayment> payments = new ArrayList<>();
HashMap<String, Object> data = new HashMap<>();
data.put("line", "34191.09107 05447.947309 71444.640008 8 84660000011631");
data.put("taxId", "38.435.677/0001-25");
data.put("scheduled", "2020-04-11");
data.put("description", "Payment for killing white walkers");
data.put("tags", new String[]{"little girl", "no one"});
payments.add(new BrcodePayment(data));

payments = BrcodePayment.create(payments);

for (BrcodePayment payment : payments){
    System.out.println(payment);
}
```

**Note**: Instead of using BrcodePayment objects, you can also pass each payment element in map format

### Query brcode payments

You can search for brcode payments using filters. 

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<BrcodePayment> payments = BrcodePayment.query(params);

for (BrcodePayment payment : payments){
    System.out.println(payment);
}
```

### Get brcode payment

To get a single BR Code payment by its id, run:

```java
import com.starkbank.*;

BrcodePayment payment = BrcodePayment.get("6532638269505536");

System.out.println(payment);
```

### Cancel a BR Code payment

You can cancel a BR Code payment by changing its status to "canceled".
Note that this is not possible if it has been processed already.

```java
import com.starkbank.*;

HashMap<String, Object> patchData = new HashMap<>();
patchData.put("status", "canceled");
BrcodePayment payment = BrcodePayment.update("5155165527080960", patchData);

System.out.println(payment);
```

### Get BR Code payment PDF

After its creation, a boleto payment PDF may be retrieved by its id. 

```java
import java.io.File;
import java.io.InputStream;
import java.nio.file.StandardCopyOption;
import com.starkbank.*;

InputStream pdf = BrcodePayment.pdf("6311252829667328");

java.nio.file.Files.copy(
    pdf,
    new File("brcode-payment.pdf").toPath(),
    StandardCopyOption.REPLACE_EXISTING
);
```

Be careful not to accidentally enforce any encoding on the raw pdf content,
as it may yield abnormal results in the final file, such as missing images
and strange characters.

### Query BR Code payment logs

Searches are also possible with BR Code payment logs:

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("paymentIds", "4785987200745472");
Generator<BrcodePayment.Log> logs = BrcodePayment.Log.query(params);

for (BrcodePayment.Log log : logs){
    System.out.println(log);
}
```

### Get BR Code payment log

You can also get a BR Code payment log by specifying its id.

```java
import com.starkbank.*;

BrcodePayment.Log log = BrcodePayment.Log.get("6532638269505536");

System.out.println(log);
```


### Pay a boleto

Paying boletos is also simple.

```java
import com.starkbank.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

List<BoletoPayment> payments = new ArrayList<>();
HashMap<String, Object> data = new HashMap<>();
data.put("line", "34191.09107 05447.947309 71444.640008 8 84660000011631");
data.put("taxId", "38.435.677/0001-25");
data.put("scheduled", "2020-04-11");
data.put("description", "Payment for killing white walkers");
data.put("tags", new String[]{"little girl", "no one"});
payments.add(new BoletoPayment(data));

payments = BoletoPayment.create(payments);

for (BoletoPayment payment : payments){
    System.out.println(payment);
}
```

**Note**: Instead of using BoletoPayment objects, you can also pass each payment element in HashMap format

### Query boleto payments

You can search for boleto payments using filters. 

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<BoletoPayment> payments = BoletoPayment.query(params);

for (BoletoPayment payment : payments){
    System.out.println(payment);
}
```

### Get boleto payment

To get a single boleto payment by its id, run:

```java
import com.starkbank.*;

BoletoPayment payment = BoletoPayment.get("6532638269505536");

System.out.println(payment);
```

### Get boleto payment PDF

After its creation, a boleto payment PDF may be retrieved by passing its id. 

```java
import java.io.File;
import java.io.InputStream;
import java.nio.file.StandardCopyOption;
import com.starkbank.*;

InputStream pdf = BoletoPayment.pdf("6311252829667328");

java.nio.file.Files.copy(
    pdf,
    new File("boleto-payment.pdf").toPath(),
    StandardCopyOption.REPLACE_EXISTING
);
```

Be careful not to accidentally enforce any encoding on the raw pdf content,
as it may yield abnormal results in the final file, such as missing images
and strange characters.

### Delete boleto payment

You can also cancel a boleto payment by its id.
Note that this is not possible if it has been processed already.

```java
import com.starkbank.*;

BoletoPayment payment = BoletoPayment.delete("5669456873259008");

System.out.println(payment);
```

### Query boleto payment logs

Searches are also possible with boleto payment logs:

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("paymentIds", "4785987200745472");
Generator<BoletoPayment.Log> logs = BoletoPayment.Log.query(params);

for (BoletoPayment.Log log : logs){
    System.out.println(log);
}
```


### Get boleto payment log

You can also get a boleto payment log by specifying its id.

```java
import com.starkbank.*;

BoletoPayment.Log log = BoletoPayment.Log.get("6532638269505536");

System.out.println(log);
```

### Investigate a boleto

You can discover if a StarkBank boleto has been recently paid before we receive the response on the next day.
This can be done by creating a BoletoHolmes object, which fetches the updated status of the corresponding
Boleto object according to CIP to check, for example, whether it is still payable or not. The investigation
happens asynchronously and the most common way to retrieve the results is to register a "boleto-holmes" webhook
subscription, although polling is also possible. 

```java
import com.starkbank;
List<BoletoHolmes> holmes = new ArrayList<>();
HashMap<String, Object> dataHolmes = new HashMap<>(); 
dataHolmes.put("boletoId", "5976467733217280");
holmes.add(new BoletoHolmes(dataHolmes));

holmes = BoletoHolmes.create(holmes);

for (BoletoHolmes sherlock : holmes){
    System.out.println(sherlock);
}
```

**Note**: Instead of using BoletoHolmes objects, you can also pass each payment element in map format

### Get boleto holmes

To get a single Holmes by its id, run:

```java
import com.starkbank.*;
sherlock = BoletoHolmes.get("6093880533450752")
System.out.println(sherlock)
```

### Query boleto holmes

You can search for boleto Holmes using filters. 

```java
import com.starkbank.*;
HashMap<String, Object> params = new HashMap<>();
params.put("limit", 3);
params.put("after", "2019-04-01");
params.put("before", "2030-04-30");
Generator<BoletoHolmes> holmes = BoletoHolmes.query(params);

for (BoletoHolmes sherlock : holmes){
    System.out.println(sherlock);
}
```

### Query boleto holmes logs

Searches are also possible with boleto holmes logs:

```java
import com.starkbank.*;
HashMap<String, Object> params = new HashMap<>();
params.put("limit", 3);
params.put("after", "2019-04-01");
params.put("before", "2030-04-30");
Generator<BoletoHolmes.Log> logs = BoletoHolmes.Log.query(params);

for (BoletoHolmes.Log log : logs){
    System.out.println(log);
}
```


### Get boleto holmes log

You can also get a boleto holmes log by specifying its id.

```java
import com.starkbank.*;
log = BoletoHolmes.Log.get("5350990148534272")
System.out.println(log);
```


### Pay utility bills

Its also simple to pay utility bills (such electricity and water bills) in the SDK.

```java
import com.starkbank.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

List<UtilityPayment> payments = new ArrayList<>();
HashMap<String, Object> data = new HashMap<>();
data.put("line", "83640000001 1 07540138007 0 61053026111 0 08067159411 9");
data.put("scheduled", "2020-04-11");
data.put("description", "Electricity for the Long Night");
data.put("tags", new String[]{"Energy", "Winterfell"});
payments.add(new UtilityPayment(data));

payments = UtilityPayment.create(payments);

for (UtilityPayment payment : payments){
    System.out.println(payment);
}
```

### Query utility payments

To search for utility payments using filters, run:

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<UtilityPayment> payments = UtilityPayment.query(params);

for (UtilityPayment payment : payments){
    System.out.println(payment);
}
```

### Get utility payment

You can get a specific bill by its id:

```java
import com.starkbank.*;

UtilityPayment payment = UtilityPayment.get("6532638269505536");

System.out.println(payment);
```

### Get utility payment PDF

After its creation, a utility payment PDF may also be retrieved by passing its id. 

```java
import java.io.File;
import java.io.InputStream;
import java.nio.file.StandardCopyOption;
import com.starkbank.*;

InputStream pdf = UtilityPayment.pdf("6565645839761408");

java.nio.file.Files.copy(
    pdf,
    new File("utility-payment.pdf").toPath(),
    StandardCopyOption.REPLACE_EXISTING
);
```

Be careful not to accidentally enforce any encoding on the raw pdf content,
as it may yield abnormal results in the final file, such as missing images
and strange characters.

### Delete utility payment

You can also cancel a utility payment by its id.
Note that this is not possible if it has been processed already.

```java
import com.starkbank.*;

UtilityPayment payment = UtilityPayment.delete("5669456873259008");

System.out.println(payment);
```

### Query utility bill payment logs

You can search for payment logs by specifying filters. Use this to understand the
bills life cycles.

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("paymentIds", "6683343345156096");
Generator<UtilityPayment.Log> logs = UtilityPayment.Log.query(params);

for (UtilityPayment.Log log : logs){
    System.out.println(log);
}
```

### Get utility bill payment log

If you want to get a specific payment log by its id, just run:

```java
import com.starkbank.*;

UtilityPayment.Log log = UtilityPayment.Log.get("6532638269505536");

System.out.println(log);
```

### Create transactions

To send money between Stark Bank accounts, you can create transactions:

```java
import com.starkbank.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

List<Transaction> transactions = new ArrayList<>();
HashMap<String, Object> data = new HashMap<>();
data.put("amount", 10000);
data.put("receiverId", "5651751147405412");
data.put("description", "A Lannister always pays his debts");
data.put("externalId", "my_unique_id");
data.put("tags", new String[]{"lannister", "debts"});
transactions.add(new Transaction(data));

transactions = Transaction.create(transactions);

for (Transaction transaction : transactions){
    System.out.println(transaction);
}
```

**Note**: Instead of using UtilityPayment objects, you can also pass each payment element in HashMap format

### Query transactions

To understand your balance changes (bank statement), you can query
transactions. Note that our system creates transactions for you when
you receive boleto payments, pay a bill or make transfers, for example.

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<Transaction> transactions = Transaction.query(params);

for (Transaction transaction : transactions){
    System.out.println(transaction);
}
```

### Get transaction

You can get a specific transaction by its id:

```java
import com.starkbank.*;

Transaction transaction = Transaction.get("5155966664310784");

System.out.println(transaction);
```

### Create payment requests to be approved by authorized people in a cost center 

You can also request payments that must pass through a specific cost center approval flow to be executed.
In certain structures, this allows double checks for cash-outs and gives time to load your account
with the required amount before the payments take place.
The approvals can be granted at our web banking and must be performed according to the rules
specified in the cost center.

**Note**: The value of the centerId parameter can be consulted by logging into our web banking and going
to the desired cost center page.

```java
import com.starkbank.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

HashMap<String, Object> paymentData = new HashMap<>();
paymentData.put("amount", 100000000);
paymentData.put("bankCode", "341");
paymentData.put("branchCode", "2201");
paymentData.put("accountNumber", "76543-8");
paymentData.put("taxId", "594.739.480-42");
paymentData.put("name", "Daenerys Targaryen Stormborn");
Transfer payment = new Transfer(paymentData);

List<PaymentRequest> requests = new ArrayList<>();
HashMap<String, Object> data = new HashMap<>();
data.put("centerId", "5967314465849344");
data.put("payment", payment);
data.put("due", "2020-04-11");
data.put("tags", new String[]{"daenerys", "invoice/1234"});
requests.add(new PaymentRequest(data));

requests = PaymentRequest.create(requests);

for (PaymentRequest request : requests){
    System.out.println(request);
}
```

**Note**: Instead of using PaymentRequest objects, you can also pass each request element in HashMap format

### Query payment requests

To search for payment requests, run:

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("centerId", "5967314465849344");
params.put("after", "2020-04-01");
params.put("limit", 10);
Generator<PaymentRequest> requests = PaymentRequest.query(params);

for (PaymentRequest request : requests){
    System.out.println(request);
}
```

### Create a webhook subscription

To create a webhook subscription and be notified whenever an event occurs, run:

```java
import com.starkbank.*;
import java.util.HashMap;

HashMap<String, Object> data = new HashMap<>();
data.put("url", "https://winterfell.westeros.gov/events-from-stark-bank");
data.put("subscriptions", new String[]{"boleto", "boleto-payment", "transfer", "utility-payment"});
Webhook webhook = Webhook.create(data);

System.out.println(webhook);
```

**Note**: Instead of using Transaction objects, you can also pass each transaction element in HashMap format

### Query webhooks

To search for registered webhooks, run:

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;

Generator<Webhook> webhooks = Webhook.query();

for (Webhook webhook : webhooks){
    System.out.println(webhook);
}
```

### Get webhook

You can get a specific webhook by its id.

```java
import com.starkbank.*;

Webhook webhook = Webhook.get("5730174175805440");

System.out.println(webhook);
```

### Delete webhook

You can also delete a specific webhook by its id.

```java
import com.starkbank.*;

Webhook webhook = Webhook.delete("6699417864241152");

System.out.println(webhook);
```

### Process webhook events

Its easy to process events that arrived in your webhook. Remember to pass the
signature header so the SDK can make sure its really StarkBank that sent you
the event.

```java
import com.starkbank.*;

Request request = Listener.listen(); // this is the method you made to get the events posted to your webhook

String content = request.content.toString();
String signature = request.headers.get("Digital-Signature");

Event event = Event.parse(content, signature);
switch (event.subscription) {
    case "transfer": {
        Transfer.Log log = ((Event.TransferEvent) event).log;
        System.out.println(log.transfer);
        break;
    }
    case "boleto": {
        Boleto.Log log = ((Event.BoletoEvent) event).log;
        System.out.println(log.boleto);
        break;
    }
    case "boleto-payment": {
        BoletoPayment.Log log = ((Event.BoletoPaymentEvent) event).log;
        System.out.println(log.payment);
        break;
    }
    case "utility-payment": {
        UtilityPayment.Log log = ((Event.UtilityPaymentEvent) event).log;
        System.out.println(log.payment);
        break;
    }
}
```

### Query webhook events

To search for webhooks events, run:

```java
import com.starkbank.*;
import com.starkbank.utils.Generator;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("isDelivered", false);
params.put("after", "2020-04-01");
params.put("before", "2020-04-30");
Generator<Event> events = Event.query(params);

for (Event event : events){
    System.out.println(event);
}
```

### Get webhook event

You can get a specific webhook event by its id.

```java
import com.starkbank.*;

Event event = Event.get("5730174175805440");

System.out.println(event);
```

### Delete webhook event

You can also delete a specific webhook event by its id.

```java
import com.starkbank.*;

Event event = Event.delete("6312789471657984");

System.out.println(event);
```

### Set webhook events as delivered

This can be used in case you've lost events.
With this function, you can manually set events retrieved from the API as
"delivered" to help future event queries with `isDelivered=false`.

```java
import com.starkbank.*;
import java.util.HashMap;

HashMap<String, Object> params = new HashMap<>();
params.put("isDelivered", true);
Event event = Event.update("5824181711142912", params);

System.out.println(event);
```

## Handling errors

The SDK may raise one of four types of errors: __InputErrors__, __InternalServerError__, __UnknownException__, __InvalidSignatureException__

__InputErrors__ will be raised whenever the API detects an error in your request (status code 400).
If you catch such an error, you can get its elements to verify each of the
individual errors that were detected in your request by the API.

For example:

```java
import com.starkbank.*;
import com.starkbank.error.InputErrors;
import com.starkbank.error.ErrorElement;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

List<Transaction> transactions = new ArrayList<>();
HashMap<String, Object> data = new HashMap<>();
data.put("amount", -200);
data.put("receiverId", "5651751147405412");
data.put("description", ".");
data.put("externalId", "repeated_id");
data.put("tags", new String[]{"Error", "Example"});
transactions.add(new Transaction(data));

try {
    Transaction.create(transactions);
} catch (InputErrors e) {
    for (ErrorElement error : e.errors){
        System.out.println(error.code);
        System.out.println(error.message);
    }
}
```

__InternalServerError__ will be raised if the API runs into an internal error.
If you ever stumble upon this one, rest assured that the development team
is already rushing in to fix the mistake and get you back up to speed.

__UnknownException__ will be raised if a request encounters an error that is
neither __InputErrors__ nor an __InternalServerError__, such as connectivity problems.

__InvalidSignatureException__ will be raised specifically by starkbank.event.parse()
when the provided content and signature do not check out with the Stark Bank public
key.
