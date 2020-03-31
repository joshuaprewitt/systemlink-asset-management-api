# SystemLink Asset Management API
The SystemLink Asset Management service supports tracking NI and 3rd party assets (devices under test, test fixtures, assets not discovered by MAX).  Some 3rd party assets like GPIB, USB-TMC, and LXI instruments are automatically discovered by NI-VISA, but you can use the SystemLink Asset Management API to programmatically add additional assets that are not automatically discovered.  In addition, the API can be used to add additional information like calibration history to existing 3rd party assets.

The primary mechanism to added additional assets is to define one or more assets in one or more JSON files (*.json) on the tester under ````C:\ProgramData\National Instruments\Skyline\Data\Assets\UserDefined\```` on Windows or ````/etc/natinst/niskyline/Data/Assets/UserDefined```` on NI Linux RT.  As an example, copy the myAssets.json file from this project to ````C:\ProgramData\National Instruments\Skyline\Data\Assets\UserDefined```` and using the Windows Service restart the NI Salt Minion service.  Once the target reconnects to the SystemLink Server you should see two new assets show up in the Asset Manager application.  Changes to the myAssets.json file should be reflected on the server on a 5 minute interval or anytime the NI Salt Minion service is restarted.

Below are the properties that can be specified in the JSON file for an asset. Every asset must contain the **bold** properties to uniquely identify them.
* **serialNumber** - string
* **modelName\*** - string
* **modelNumber\*** - integer
* **vendorName\*** - string
* **vendorNumber\*** - integer
* busType - string
    * Possible values: "BuiltInSystem" (default if not specified or specified with an invalid value), "PciPxi", "Usb", "Gpib", "Vxi", "Serial", "TcpIp", "cRio", "Scxi", "cDaq", "SwitchBlock", "Scc", "FireWire", "Accessory", "Can", "SwitchBlockDevice"
* assetClass - string
    * Possible values: "Asset", "Dut", "Fixture"
    * Not specifying this property will default to "Undefined" asset class
* assetName- string
* firmwareVersion - string
* hardwareVersion - string
* visaResourceName - string
* location
    * slotNumber - integer
    * parent - string
    * resourceUri - string
* supportsSelfCalibration - boolean
* supportsExternalCalibration - boolean
* selfCalibration
    * isLimited - boolean
    * calibrationDate - string
    * operator
        * displayName - string
        * operatorUserId - string
* externalCalibration
    * isLimited - boolean
    * calibrationDate - string
    * comments - string
    * checksum - string
    * recommendedInterval - integer
    * nextRecommendedDate - string
    * operator
        * displayName - string
        * operatorUserId - string
 
\* At least one of **ModelName** or **ModelNumber** properties should be specified. At least one of **VendorName** or **VendorNumber** properties should be specified. Further updates to your already stored 3rd party assets will be made only if the same set of Model and Vendor properties will be saved in the JSON file.

## Asset JSON Schema
````json
{
    "assets": [
        {
            "serialNumber": "12345",
            "modelName": "Virtual Thermal Asset",
            "modelNumber": 12345678,
            "vendorName": "Acme",
            "vendorNumber": 12345,
            "busType": "TCP",
            "assetClass": "Asset",
            "assetName": "Virtual Thermal Asset 1",
            "firmwareVersion": "1.0A",
            "hardwareVersion": "1.0.12",
            "supportsSelfCalibration": true,
            "supportsExternalCalibration": true,
            "selfCalibration": {
                "isLimited": false,
                "date": "2010-07-10T13:06:18Z",
                "operator": {
                    "displayName": "John Doe",
                    "userId": "nijohndoe"
                }
            },
            "externalCalibration": {
                "isLimited": false,
                "date": "2014-04-05T13:55:27Z",
                "comments": "First Calibration",
                "checksum": "",
                "recommendedInterval": 24,
                "operator": {
                    "displayName": "John Doe",
                    "userId": "nijohndoe"
                }
            }
        },
        {
            "serialNumber": "01BB877B",
            "modelName": "Battery Test Fixture",
            "modelNumber": 19499429,
            "vendorName": "Acme",
            "vendorNumber": 1234569,
            "assetClass": "Fixture",
            "assetName": "Fixture 1234",
			"location": {
				"slotNumber": 1
			}
        }
    ]
}
````


## LabVIEW Example
[AssetCreationDemoApplication](https://github.com/joshuaprewitt/systemlink-asset-management-api/tree/master/AssetCreationDemoApplication) is a LabVIEW example that illustrates how to add additional 3rd party assets to a test system. This example requires the SystemLink Client to be installed and the client to have been added and approved to a SystemLink Server.  After the application has run it may take up to 5 minutes for the change to be reflected on the server.
