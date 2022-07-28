---
title: Post di prova
date: 2022-07-28T08:21:49.652Z
description: asdadasdasdasd
image: ""
---
# Integrating a Paddle License Key

## What is Paddle and Why Integrate it?

![](img/1-hd_mqjkirfrcpmdcbu5taq.png)

Paddle.com is **a digital software platform that allows developers to sell their products in the form of a subscription or one-time purchase**. The website also provides e-commerce, marketing, and analysis services to developers and businesses as well.

As a developer in the Apple eco system, you are probably familiar with Apple's **in-app purchase** system that allows you to configure your application to enable one-time purchase or subscriptions that will unlock additional features.  This requires a lot of setup on **App Store Connect** and in your code to facilitate the purchase.  **Glassfy** is a great third party solution to simplify the process as described in this post [Adding In-app Subscriptions to your iOS app using Glassfy, SwiftUI, and Swift Concurrency](https://blog.glassfy.io/adding-in-app-subscriptions-to-your-ios-app-using-glassfy-swiftui-and-swift-concurrency-c77ae2ce2351)

**Paddle**, on the otherhand provides you a way to bypass that process entirely if you like by giving your users the ability to enter a **licence key** in your app that will unlock the added features.

Using the **Glassfy SDK** you are able to add **Paddle** integration **as well as** Apple's own in-app purchase system to your app.

For example, you may have already implemented an in-app subscription for your app but you want a way to allow new users, or those with lapsed subscriptions, the ability to test out that feature for a limited time before they decide if they want to make the subscription purchase.  Or perhaps you don't want to offer the features at all through through an in-app purchase, and make that purchase available elsewhere, outside of the app.

This is where Paddle comes in.  With Paddle, you generate license codes that your users can retrieve through a link to the Paddle site from your own web domain.  These codes may be offered for free, or at a cost.  It is up to you.

You provide a mechanism within yoru app that will allow them to enter the code they receive from Paddle to unlock the feature.

## Paddle Setup

After obtaining your  [Paddle](https://www.paddle.com) account and it, and your domain, has been verified, you can set up your products.

### New Product

From the **Catalog**  link on the Paddle account page, select **Products** and create a **new Product**.

1. Enter your product name and other information as described in the paddle documentation.
2. For the **Fulfillment Method** select **License List - send a license key from an uploaded list**.

### Release Screen

On the next screen, you need to do **three** things.

1. Create a simple text file with a number of unique licence codes.
   **Note:** These codes must not only be unique within your own app, but across the entire Paddle system world-wide so it is recommended that you prefix your code with the first letters of your app.
2. You are not uploading any application, but rather, you should enter the **App Store URL** for your app into this field and **Click to Save**.\
   When the user receives the licence code via email, they will also be directed to that app on the App Store so they can install it.
3. Click on the **Go to prices manager** button and either:

   a. set the price to be **free** or

   b. set a price

Once his information has been entered and confirmed, all of the indicators will be green and you can release your product.

#### Product ID

If you return to the sidebar and select **Catalog > Products** you will find all of your products listed. The **Product ID** will be required in the next step, so make note of that.  

Observe the status and price point for your app.  In this example, the product is **active** and **free**.

## Glassfy Setup

In **Glassfy**, select your app and click on the **Products** tab.  You will need to add a **SKU** and set the **Permission** for this sku.

### Create Sku

Create a new sku and:

1. Enter a unique **SKU ID** for your product.
2. Click on **Add** **Paddle Product ID** and select **License Code.**
3. Enter your Product ID as copied from the product listing on Paddle.
4. Choose the duration of the license.\
   If you wish the license to never expire, leave it at 0 days.

### Create Permission

As with Store Kit skus, Paddle skus need to be added to a permission, so either create a new one or add it to an existing permission that you may already have set up for Store Kit in-app purchases.  In this example, I have created a unique sku for the Paddle licences that will essentially be equivalent to a single, non- renewing one-month Store-Kit in-app subscription.  This sku has been added to the *allFeatures* permission that also applies to the StoreKit in-app subscriptions.  There will be no charge to the user for using this code, as the prices was set to **free**, but after 30 days, the code will no longer be applied to the permission.

## Getting the License File

Paddle will provide you with a **checkout link** that you can add to your own authorized domain web site.  Currently, Apple does not allow you to direct users within your app to go to that site to make a purchase, so you will have to find a way to promote the licence availability.

When they access the link, they will be asked to enter their email address, and, if it is a paid purchase, provide payment details.  

Once they make the "purchase" they will receive an email containing the Licence Key and the button is a link to application in the App Store.

## Coding your Project

Where and how you implement or facilitate the entry of the license fee within your application is up to you.  You will need to create a button or link that will provide the user with a text entry field where they can enter the License Key code they received in the email and an action associated with the entry of that text that will verify it as being valid.

All that is required is a simple **Glassfy.connectPaddle** function, that has one argument; the license key.  If no error is found, you can proceed with providing the user with the features granted by the permission.

#### Completion Handler Version

```swift
let licenceKeyEntered = "GT-LVrAj6Yc1"
Glassfy.connectPaddle(licenseKey: licenceKeyEntered) { error in
    if let error = error {
        print(error)
      	// Alert user
    }
    // Now that permission has been granted, unlock the feature
}
```

#### Asynchronous Version

```swift
let licenceKeyEntered = "GT-LVrAj6Yc1"
Task {
    do {
        try await Glassfy.connectPaddle(licenseKey: licenceKeyEntered)
        // Now that permission has been granted, unlock the feature
        }
    } catch {
        print(error)
  			// Alert user
    }
}
```