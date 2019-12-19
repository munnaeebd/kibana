# kibana

Issue: Create Index patterns forbidden error. 

If your storage is low kibana auto changes its config to read-only mode. to deal with it, go to your dev tools console and
run below command:

open kibana-->dev tools--> console

PUT .kibana/_settings
{
"index": {
"blocks": {
"read_only_allow_delete": "false"
}
}
}



