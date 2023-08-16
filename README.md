# Desk Connector
## Contact 
Contact desk-connector-feedback@google.com for questions and feedbacks.

## Codelab
Try out this [codelab](https://codelabs.developers.google.com/deskapi) to interact with desk connector directly.  

## Integration Guide
### Enterprise enrollment:
#### Before you begin
* Make sure you have access to the [Google Admin console](https://admin.google.com). The Admin console is only available when you're signed in to an admin account. If you don't have access to an admin account, get help from someone else who does. For details, see [Who is my administrator?](https://support.google.com/a/answer/6208960?sjid=5010985786185906113-NA)
* [Enroll ChromeOS devices](https://support.google.com/chrome/a/answer/1360534?sjid=5010985786185906113-NA) in your domain.
* Ensure that devices have ChromeOS version 108 or later.

#### Set up the ChromeOS Desk connector on devices
1. In the Admin console, go to **Menu** > **Device** > **Chrome** > **Settings** > **Users & browsers**.
1. To configure the ChromeOS Desk connector for all devices, leave the top organizational unit selected. Otherwise, select a child [organizational unit](https://support.google.com/a/topic/1227584?sjid=5010985786185906113-NA).
1. Scroll to **Desk API**.
1. For **Desk API for third-party ChromeOS desk control**, select **Enable Desk API for third-party ChromeOS desk control**.
1. Click **Save**.

#### Verify policies are applied
After you apply any Chrome policies, users need to restart Chrome for the settings to take effect. You can check users’ devices to make sure the policy was applied correctly.

1. On a managed device, go to **chrome://policy**.
1. Click **Reload policies**.
1. For **DeskAPIThirdPartyAccessEnabled**:
   1. Make sure **Status** is **OK**.
   1. Click **Show more** and make sure **Value** is set to **True**.
1. For DeskAPIThirdPartyAllowlist:
    1. Make sure **Status** is **OK**.
    1. Click **Show more** and make sure that the value fields are the same as what you set for **Enable Desk API for a list of third-party domain** in the Google Admin console.
    
You can use Inspect Console to view errors that the browser reports as the third-party partner’s page loads.

1. On a managed device, go to the URL that you entered for **Enable Desk API for a list of third-party domain**. 
1. Click **Console**.
1. Type **chrome.runtime**.
1. Verify the object is not **undefined**.


### **API description:**


#### **LaunchDesk**


Use the `LaunchDesk` API to programmatically launch a new empty desk and switch to it. 


#### **RemoveDesk**


Use the `RemoveDesk` API to programmatically remove a desk. It can either close all windows on the desk or combine windows to the desk on the left.


#### **SetWindowProperties**

Use the `setWindowProperties` API to programmatically set properties of the host window of the web page.


#### **GetActiveDesk**

Use the `GetActiveDesk` API to programmatically query the current active desk. 


#### **SwitchDesk**

Use the `SwitchDesk` API to programmatically switch to an appointed desk.

========================
#### **Desk Events**
Consume a set of desk events to get notified when there is a desk being added/removed or switched.


### **Check ChromeOS version:**

Desk API feature is rolled out incrementally. Check below schedule for the availability of individual API method.
The same binary will be released to beta channel first and to stable channel approximately a month later.  


<table>
  <tr>
   <td>API method
   </td>
   <td>ChromeOS version
   </td>
   <td>Beta release date 
   </td>
   <td>Stable release date
   </td>
  </tr>
  <tr>
   <td style="background-color: #93c47d">LaunchDesk, RemoveDesk
   </td>
   <td style="background-color: #93c47d">104.* and above
   </td>
   <td style="background-color: #93c47d">Jun 23, 2022
   </td>
   <td style="background-color: #93c47d">Aug 4, 2022
   </td>
  </tr>
  <tr>
   <td style="background-color: #93c47d">SetWindowProperties
   </td>
   <td style="background-color: #93c47d">105.* and above
   </td>
   <td style="background-color: #93c47d">Jul 27, 2022
   </td>
   <td style="background-color: #93c47d">Sep 1, 2022
   </td>
  </tr>
  <tr>
   <td style="background-color: #93c47d">GetActiveDesk, SwitchDesk
   </td>
   <td style="background-color: #93c47d">108.* and above
   </td>
   <td style="background-color: #93c47d">Oct 27, 2022
   </td>
   <td style="background-color: #93c47d">Dec 1, 2022
   </td>
  </tr>
</table>
Update ChromeOS to latest version to get full access feature.


### **Regular API Development**


#### Syntax


```
chrome.runtime.sendMessage(
  "kflgdebkpepnpjobkdfeeipcjdahoomc",        //[extensionId], string, this is a constant                         
                                             //use it for all Desk API   
                                             //calls.
  {
    "messageType":"",                        //[messageType], string, 
                                             //defines the API method  
                                             //to invoke.                                                             

    "operands":{}                            //[operands], object,    
                                             // parameters provided to 
                                             // the API.
  },
  callback);                                //[callback], function, to be   
                                            //executed after API returned.
```



##### **Request Parameters**

`messageType`


```
enum messageType {
  LaunchDesk = "LaunchDesk",                    // Launch a desk.
  RemoveDesk = "RemoveDesk"                     // Remove a desk.
  SetWindowProperties = "SetWindowProperties"   // Set window properties.  
  GetAcitveDesk = "GetActiveDesk"               // Retrieve the active desk.
  SwitchDesk = "SwitchDesk"                     // Switch to the appointed desk.                                         
}   
```


`operands`
```
LaunchDeskOperands {
   optional DOMString deskName;     // string. User readable name of the 
                                    // desk. Optional. If provided, use it    
                                    // as the desk name, otherwise use an   
                                    // auto-generated  desk name.
};
```

```
RemoveDeskOperands {
   DOMString deskId;  // string. Unique identifier of the desk to be removed.
   optional RemoveDeskOptions removeDeskOptions;   // object. Additional       
                                              // remove desk options.   
                                              // Optional. If not provided   
                                              // it will default to close    
                                              // all windows on the desk.
  optional bool skipConfirmation;//Optional. When provided, skip the    
                              // confirmation window and directly remove     
                              // the desk.
  optional ConfirmationSetting confirmationSetting; // Optional. When provided, use the customized confirmation window instead of the default window.

   } 
   
   
RemoveDeskOptions {
   boolean combineDesks;        // boolean. If `true` combine them to the active desk to the left, otherwise close all windows on the desk.
  };

ConfirmationSetting {
  DOMString title;             // Confirmation window title.
  DOMString message;           // Confirmation window message.
  DOMString iconUrl;           // Confirmation window Url. Only data Url is       
                               // accepted.
  DOMString acceptMessage;     // The message for confirming the action.
  DOMString rejectMessage;     // The message for rejecting the action
};


```
```
SetWindowPropertiesOperands {
   WindowProperties windowProperties;  // Object. Define window properties.
}
WindowProperties {
  boolean allDesks;               // boolean. If `true`, set the window  
                                  // that contains the web page to show   
                                  // up on all desks. If `false`, set 
                                  // the window that contains the web 
                                  // page to only show up on a single desk. 
}
```
```
SwitchDeskOperands {
  DOMString deskId;                  // string. Unique identifier of the   
                                     // desk to be removed.        
}

```

##### Response


```
Response {
messageType: "OperationSuccess" | "OperationFailure" // string. Indication of if        
                                                     //the operation succeeded.
operands:{}                           // object. operation result if there is any. 
errorMessage: ""                      // string. If the operation fails, a related    
                                      // error will be returned. Otherwise it will      
                                      // be an empty string.
}
```



##### Error handling


```
Error Messages:
*   NoCurrentUserError - When there is no active user profile.
*   DesksCountCheckFailedError - Maximum number of desks reached
*   StorageError - Unable to retrieve template from local or remote storage.
*   ResourceNotFoundError - The desk with the provided desk id cannot be found in the desk list.
*   InvalidIdError - The desk id is not a valid UUID.
*   DeskBeingModifiedError - The previous desk animation hasn’t been settled. Wait and retry when seeing such errors.
```



#### Example



1. Launch an empty desk and store the identifier of the newly launched desk.

```
var deskId;                                      // Global variable. Store desk       
                                                 // identifier for later operation.   
chrome.runtime.sendMessage(
    "kflgdebkpepnpjobkdfeeipcjdahoomc", {
        "messageType": "LaunchDesk",
        "operands": {
            "deskName": "test"                   // Specify desk name.
        }
    },
    function(response) {
     if(response.ErrorMessage) {
     // Do error handling 
    }
        deskId = response.operands.deskUuid;     // Parse deskId from response.
    }
);

```


2. Remove the previous launched desk and close all windows on it

 ```
chrome.runtime.sendMessage("kflgdebkpepnpjobkdfeeipcjdahoomc", {
    "messageType": "RemoveDesk",
    "operands": {
        "deskId": deskId                        // In this example it's the same           
                                                //deskId from above launch desk 
                                                // example.       
    }
  },
  function(response) {
    if(response.errorMessage) {
     // Do error handling 
    }
    
  }
);
```



2a. Remove desk API by default comes with a confirmation window. To skip the confirmation and directly close the desk, use the below parameter:


```
chrome.runtime.sendMessage("kflgdebkpepnpjobkdfeeipcjdahoomc", {
    "messageType": "RemoveDesk",
    "operands": {
        "deskId": deskId,
        "skipConfirmation":true     
    }
  },
   if(response.errorMessage) {
     // Do error handling 
    }
);
```




3. Set the browser window that contains the web page to show up on all desks. `(Available in M105.* and above)`

```
chrome.runtime.sendMessage("kflgdebkpepnpjobkdfeeipcjdahoomc", {
    "messageType": "SetWindowProperties",
    "operands": {
        "allDesks": true
    }
   },
    if(response.errorMessage) {
     // Do error handling 
    }
);
```


4. Set the browser window that contains the web page to show up on a single desk. `(Available in M105.* and above)`

```
chrome.runtime.sendMessage("kflgdebkpepnpjobkdfeeipcjdahoomc", {
    "messageType": "SetWindowProperties",
    "operands": {
        "allDesks": false
    }
   },
   function(response) {
        if(response.ErrorMessage) {
     // Do error handling 
    }
  }
);
```


5. Query the current active desk and store the deskId. `(Available in M108.* and above)`

```
let deskId1;
chrome.runtime.sendMessage("kflgdebkpepnpjobkdfeeipcjdahoomc", {
    "messageType": "GetActiveDesk",
    },
   function(response) {
        if(response.ErrorMessage) {
     // Do error handling 
    }
    deskId1 = response.operands.deskUuid;        // Parse deskId from response.
  }
);
```


6. Switch to the appointed desk. `(Available in M108.* and above)`

```
chrome.runtime.sendMessage("kflgdebkpepnpjobkdfeeipcjdahoomc", {
    "messageType": "SwitchDesk",
    "operands": {
        "deskId": "CHANGE_ME"                   // Change the deskId to the Id 
                                                // of the target desk. The ID can   
                                                // be obtained from GetActiveDesk        
                                                // API or LaunchDesk API response.
    }
   },
   function(response) {
        if(response.ErrorMessage) {
     // Do error handling 
    }
  }
);
```
### **Events API development**
#### Syntax
```
// Initialize a two-dimensional channel 
const port = chrome.runtime.connect("kflgdebkpepnpjobkdfeeipcjdahoomc");


// Define your listener
function f = ....

// Add a listener
port.onMessage.addListener(f);

// Remove a listener
port.onMessage.removeListener(f);
```

#### Events payload
```
DeskAddedEvents:
{
 eventName: "DeskAdded",
 data: {
  deskId: REAL_DESK_ID,
}

DeskRemovedEvents:
{
 eventName: "DeskRemoved",
 data: {
  deskId: REAL_DESK_ID,
 }
}

DeskSwitchedEvents:
{
eventName: "DeskSwitched",
data:{
  activated: DESK_BEING_ACTIVATED,
  deactivated: DESK_BEING_DEACTIVATED,
 }
}
```

#### Example
```
const port = chrome.runtime.connect("kflgdebkpepnpjobkdfeeipcjdahoomc");


// Define your listener
const f = (event) => {
 if (event.eventName == "DeskAdded") {
 // Your own logic to handle desk added events;
 console.log("DeskAdded");
console.log("new deskId is: " + event.data.deskId);
}
if (event.eventName == "DeskRemoved") {
 // Your own logic to handle desk removed events.
 console.log("DeskRemoved");
console.log("removed deskId is: " + event.data.deskId);
}
 if (event.eventName == "DeskSwitched") {
 // Your own logic to handle desk switched events.
  console.log("DeskSwitched");
  console.log("old desk id is: " + event.data.deactivated);
  console.log("new desk id is: " + event.data.activated);
}
};

// Add a listener
port.onMessage.addListener(f);
```




### Caveats


1. Desk action throttling

The animation for Desk launch, remove, and switch takes 1-2 seconds to settle. Back-to-back desk operation will be throttled. For example, if you launch a desk, and then remove the desk immediately before the first animation settles (&lt;1 second), the second operation will fail. 

To handle this exception: retries with an interval on the DeskBeingModifiedError. 
