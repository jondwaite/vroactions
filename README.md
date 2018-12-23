# vroactions
Workflow actions for VMware vRealize Orchestrator

I got frustrated by the poor support 'out of the box' for vRealize Orchestrator (with the vCloud Director plugin) to manipulate metadata on vCloud Director objects. The built-in methods appear to have some serious bugs, and do not allow for manipulation of several key properties (e.g. non-String metadata types, Private and ReadOnly entries and deletion of existing metadata values). Since we use vCloud metadata to track several key properties of the vApps and VMs in our environment I set about writing some vRO workflow actions which would fix these issues.

Each .action file can be imported into an Action library in vRealize Orchestrator and then used individually or collectively as required. The sections below detail the parameters and actions for each module.

//TODO - Add description for each action.
