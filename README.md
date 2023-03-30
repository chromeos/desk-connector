# Desk Connector Integration Guide
### Eligibility Criteria:
The web page hosting the JS for the code below should be served from https://[allowlisted domain]. 
*   HTTP (non-secure) pages will not be able to send messages to Desk Connector.
*   Pages from non-allowlisted domains will not be able to send messages to Desk Connector.

Please reach out for eligibility issue. 


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


### **Getting Access:**


Install [Desk Connector Demo](https://chrome.google.com/webstore/detail/desk-connector-demo/oagemgapjncacjdaockjfaidedggjond?hl=en&authuser=0) extension from web store


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


### **Development**


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



* 2a. Remove desk API by default comes with a confirmation window. To skip the confirmation and directly close the desk, use the below parameter:


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




### Debugging



1. Incognito window.

To have the integration work in an **incognito window**, the extension should be set to allow incognito. Follow the below to allow it: 



* Navigate to chrome://extensions and open extension details page. 



* Toggle on the “allow in incognito” option.



2. Validate extension.

To check whether the extension has been installed correctly, follow the steps:



1. Navigates to chrome://extensions and clicks **service worker**.

2. On the new window, go to the **Console** tab.
3. print out the value of `chrome.wmDesksPrivate` to console.
4. If you see the result returned (i.e should not be undefined), then extension is installed properly.


### Known issue



*   **Desk action throttling**

The animation for Desk launch, remove, and switch takes 1-2 seconds to settle. Back-to-back desk operation will be throttled. For example, if you launch a desk, and then remove the desk immediately before the first animation settles (&lt;1 second), the second operation will fail. 

To handle this exception: retries with an interval on the DeskBeingModifiedError. 
