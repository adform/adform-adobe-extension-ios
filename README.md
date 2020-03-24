# Publishing events using Adobe Experience SDK

Track marketing events for Adform platform using Adobe experience SDK.

## Features

- All events sent to Adobe platform will be routed to Adform as well
- Easily convert Adform's `TrackPoint` to Adobe events

## Requirements

- iOS 10
- Adobe Core Libraries
- Adform Tracking SDK

## Set-up Adobe experience platform

- Set-up Adobe experience patform
- Then install `Adform mobile extenesion` in Adobe experience cloud platform

## Integerate Adform extension

### 1. Using CocoaPods

We are using CocaPods for dependency management. Therefore, please add this pod to your podfile:

```
pod 'AdformAdobeExtension'
```

Then run `pod install` or `pod update` to install the latest version of the extension.

### 2. Manual integration

If you wish to integrate this extension manually, follow these steps: 

1. Copy `AdformAdobeExtension` folder and add it to your project. 
2. Integrate required dependencies: [AdformTracking](https://github.com/adform/adform-tracking-ios-sdk) and [ACPCore](https://aep-sdks.gitbook.io/docs/getting-started/get-the-sdk). 

### 3. Register Adform extension

You should register Adform extension in `didFinishLaunchingWithOptions` app delegate method like this:

```obj-c
...
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    ...
    [AdformAdobeExtension registerExtension];
    ...
}
```

## Sending events

There are a couple of ways to send events through Adobe Experience SDK. If you already have Adform integrated you can use convenience method to convert current track points to event data or just create event data from scratch using provided key constants.

### 1. Using track point adapter

To minimize code changes needed to migrate to Adobe Experience SDK you can use `AFTEventAdapter` class to convert `AFTrackPoint` class instances to event data and send it through Adobe SDK. Following example shows you how to do it.

```obj-c
- (void)sendTrackPoint {
    AFTrackPoint *trackPoint = [[AFTrackPoint alloc] initTrackPoint:123456789];
    [trackPoint setAppName:@"DemoAdobe-iOS"];
    
    AFOrder *order = [AFOrder new];
    order.orderId = @"1";
    order.sale = 15;
    order.currency = @"Eur";
    order.orderStatus = @"Sold";
    order.country = @"Lithuania";
    order.gender = @"Male";
    [trackPoint setOrder:order];
    
    AFProduct *product1 = [AFProduct new];
    product1.categoryId = @"1";
    product1.categoryName = @"Category";
    product1.productId = @"2";
    product1.productName = @"Product";
    product1.weight = 5;
    product1.step = AFEcomerceStepView;
    product1.productCount = 3;
    [trackPoint addProduct:product];
    
    NSDictionary *data = [AFTEventAdapter makeContextDataFrom:trackPoint];
    [ACPCore trackAction: @"Demo-action" data: data];
}
```

You can always use Adobe's '.trackState()' as well, though in Adform tracking SDK it works exactly the same, as you would use '.trackAction()'.

```obj-c
- (void)sendTrackPoint {
    AFTrackPoint *trackPoint = [[AFTrackPoint alloc] initTrackPoint:123456789];
    [trackPoint setAppName:@"DemoAdobe-iOS"];
    
    NSDictionary *data = [AFTEventAdapter makeContextDataFrom:trackPoint];
    [ACPCore trackState: @"Demo-State" data: data];
}
```

### 2. Sending event data directly

If you have not created events for Adform before, there is a more generic way of sending those events:

```obj-c
- (void)sendTrackPoint {
    NSString *trackingPointId = [NSString stringWithFormat:@"%ld", [AdformAdobeExtension trackingPointId];
    NSDictionary *data = @{
        kTrackPointIdKey: trackingPointId,
        kTrackPointAppNameKey: @"DemoAdobe-iOS",
        
        kTrackPointOrderIdKey: @"1",
        kTrackPointOrderSaleKey: @"15",
        kTrackPointOrderCurrencyKey: @"Eur",
        kTrackPointOrderStatusKey: @"Sold",
        kTrackPointOrderCountryKey: @"Lithuania",
        kTrackPointOrderGenderKey: @"Male",
        
        // Product
        kTrackPointProductCategoryIdKey: @"1",
        kTrackPointProductCategoryNameKey: @"Category",
        kTrackPointProductIdKey: @"2",
        kTrackPointProductNameKey: @"Product",
        kTrackPointProductWeightKey: @"5",
        kTrackPointProductStepKey: @"1",
        kTrackPointProductCountKey: @"3"
    }
    
    [ACPCore trackAction: @"Demo-action" data: data];
}
```

# Release notes

## 1.3.3

### Initial release

## 1.3.4

### Update readme
