---
title: include file
description: include file
services: azure-communication-services
author: peiliu
manager: rejooyan

ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
---

Get started with Azure Communication Services by using the Communication Services C# SMS SDK to send SMS messages.

Completing this quickstart incurs a small cost of a few USD cents or less in your Azure account.

> [!NOTE]
> Find the finalized code for this quickstart on [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- The latest version [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) for your operating system.
- An active Communication Services resource and connection string. [Create a Communication Services resource](../../create-communication-resource.md).
- An SMS enabled telephone number. [Get a phone number](../get-phone-number.md).

### Prerequisite check

- In a terminal or command window, run the `dotnet` command to check that the .NET SDK is installed.
- To view the phone numbers associated with your Communication Services resource, sign in to the [Azure portal](https://portal.azure.com/), locate your Communication Services resource and open the **phone numbers** tab from the left navigation pane.

## Setting up

### Create a new C# application

In a console window (such as cmd, PowerShell, or Bash), use the `dotnet new` command to create a new console app with the name `SmsQuickstart`. This command creates a simple "Hello World" C# project with a single source file: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Change your directory to the newly created app folder and use the `dotnet build` command to compile your application.

```console
cd SmsQuickstart
dotnet build
```

### Install the package

While still in the application directory, install the Azure Communication Services SMS SDK for .NET package by using the `dotnet add package` command.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

Add a `using` directive to the top of **Program.cs** to include the `Azure.Communication` namespace.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## Object model

The following classes and interfaces handle some of the major features of the Azure Communication Services SMS SDK for C#.

| Name                                       | Description                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | This class is needed for all SMS functionality. You instantiate it with your subscription information, and use it to send SMS messages.                           |
| SmsSendOptions | This class provides options to configure delivery reporting. If enable_delivery_report is set to True, then an event will be emitted when delivery was successful |
| SmsSendResult               | This class contains the result from the SMS service.                                          |

## Authenticate the client

 Open **Program.cs** in a text editor and replace the body of the `Main` method with code to initialize an `SmsClient` with your connection string. The code below retrieves the connection string for the resource from an environment variable named `COMMUNICATION_SERVICES_CONNECTION_STRING`. Learn how to [manage your resource's connection string](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## Send a 1:1 SMS message

To send an SMS message to a single recipient, call the `Send` or `SendAsync` function from the SmsClient. Add this code to the end of `Main` method in **Program.cs**:

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
You should replace `<from-phone-number>` with an SMS-enabled phone number associated with your Communication Services resource and `<to-phone-number>` with the phone number you wish to send a message to.

> [!WARNING]
> Note that phone numbers should be provided in E.164 international standard format (e.g.: +14255550123). The **From** phone number may be a Short Code as well (e.g.: 23456).

## Send a 1:N SMS message with options
To send an SMS message to a list of recipients, call the `Send` or `SendAsync` function from the SmsClient with a list of recipient's phone numbers. You may also pass in optional parameters to specify whether the delivery report should be enabled and to set custom tags.

```csharp
Response<IReadOnlyList<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

You should replace `<from-phone-number>` with an SMS-enabled phone number associated with your Communication Services resource and `<to-phone-number-1>` and `<to-phone-number-2>` with phone number(s) you wish to send a message to.

> [!WARNING]
> Note that phone numbers should be provided in E.164 international standard format (e.g.: +14255550123). The **From** phone number may be a Short Code as well (e.g.: 23456).

The `enableDeliveryReport` parameter is an optional parameter that you can use to configure Delivery Reporting. This is useful for scenarios where you want to emit events when SMS messages are delivered. See the [Handle SMS Events](../handle-sms-events.md) quickstart to configure Delivery Reporting for your SMS messages.

`Tag` is used to apply a tag to the Delivery Report

## Run the code

Run the application from your application directory with the `dotnet run` command.

```console
dotnet run
```

## Sample Code

You can download the sample app from [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)
