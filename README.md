# vroactions
vRealize Orchestrator Workflow actions for vCloud Director

I got frustrated by the poor support 'out of the box' for vRealize Orchestrator (with the vCloud Director plugin) to manipulate metadata on vCloud Director objects. The built-in methods appear to have some serious bugs, and do not allow for manipulation of several key properties (e.g. non-String metadata types, Private and ReadOnly entries and deletion of existing metadata values). Since we use vCloud metadata to track several key properties of the vApps and VMs in our environment I set about writing some vRO workflow actions which would fix these issues. In order to do this, the attached actions use the vCloud Director API interface accessed via the VclHost parent of the object being accessed and using standard HTTPS requests.

Each .action file can be imported into an Action library in vRealize Orchestrator and then used individually or collectively as required. The sections below detail the parameters and actions for each module.

The source folder contains the javascript listings for the code contained in each Action for reference.

## Supported Versions

The actions do not require any of the actions included in the VMware vRO plugin for vCD, but in order to be able to add vCD endpoints to the UI in vRO and select objects (e.g. browse for a VM object to pass this to the workflow) you will need to have some version of the plugin installed and configured.

The specific versions used in development/testing were:
vRealize Orchestrator v7.5 (Build 7.5.0.10044239)
vCloud Director v9.5.0.1 (Build 9.5.0.11038232)
vCloud Director 9.5 plugin for vRealize Operations (Build 10198325)

Any versions of vCloud Director from 5.1 up (5.6, 8.0, 8.10, 8.20, 9.0, 9.1 and 9.5) should work as the metadata API is consistent for these, however all testing has been done against v9.5. Would appreciate feedback on compatibility here.

Any versions of vRealize Orchestrator that support a vCloud Director plugin should also work, testing has been performed on vRO v7.5 so again any feedback on compatible (or incompatible) versions appreciated.

## getVcloudMetadata Action

This action returns the value of the specified vCloud metadata entry. It should work for any valid vCloud Director object reference but has been mainly tested on VM and vApp objects. Rather than request all metadata entries and then attempt to match the 'key' it only retrieves the requested key value from the vCD API.

#### Input Parameters:

|  Name  |  Type  | Required? | Default | Description |
|:------:|:------:|:---------:|:-------:|:------------|
| object | Any    |    Yes    |  None   | Any valid vCloud Director object from which to obtain the metadata value |
| key    | string |    Yes    |  None   | The metadata key to match |
| domain | string |    No     |  None   | The domain for the key - specify as 'SYSTEM' to retrieve provider-only values |

#### Output:

A `string` value is returned if the requested metadata key exists and has data (regardless of data type a `string` is returned and you will need to convert this to the appropriate data type if needed).

## deleteVcloudMetadata Action

This action deletes the specified vCloud metadata key/value entry. It should work for any valid vCloud Director object reference but has been mainly tested on VM and vApp objects. If `waitForTask` is set to `true` the action will not terminate until the metadata deletion has completed (or failed).

#### Input Parameters:

|    Name     |   Type  | Required? | Default | Description |
|:-----------:|:-------:|:---------:|:-------:|:------------|
| object      | Any     |    Yes    |  None   | Any valid vCloud Director object in which to delete the metadata value |
| key         | string  |    Yes    |  None   | The metadata key to delete |
| domain      | string  |    No     |  None   | The domain for the key - specify as 'SYSTEM' to delete provider-only values |
| waitForTask | boolean |    No     | false   | Whether to return immediately or wait until the delete task has completed |

#### Output:

A `vCloud:Task` object is returned if the deletion request is successfully submitted, otherwise `null` is returned.

## setVcloudMetadata Action

This action creates (or updates an existing) metadata value on the specified vCloud Director object. The input value is type-converted automatically based on the dataType supplied. The special value `now` can be supplied for the `DateTime` data type to set a value with the current date and time. If `waitForTask` is set to `true` the action will wait for the metadata update to complete before terminating.

#### Input Parameters:

|    Name     |   Type  | Required? | Default | Description |
|:-----------:|:-------:|:---------:|:-------:|:------------|
| object      | Any     |    Yes    |  None   | Any valid vCloud Director object in which to update the metadata value |
| key         | string  |    Yes    |  None   | The metadata key to create or update |
| value       | string  |    Yes    |  None   | The value to create for the specified key |
| dataType    | string  |    No     | string  | The type of value to be created, one of 'String','Number','DateTime' or 'Boolean'. If not specified will create a 'String' metadata value. Type conversion will be automatically performed on the `value` supplied to the specified type.
| readOnly    | boolean |    No     | false   | Specifies that the metadata entry should be created as Read-Only for the vCloud tenant |
| hidden      | boolean |    No     | false   | Specifies that the metadata entry will be created as hidden from the vCloud tenant and will only be visible to the System (Service Provider) context |
| waitForTask | boolean |    No     | false   | Whether to return immediately or wait until the metadata create/update task has completed |

#### Output:

A `vCloud:Task` object is returned if the create/update request is successfully submitted, otherwise `null` is returned.
