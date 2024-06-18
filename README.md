<script type="text/javascript" src="/_layouts/15/sp.runtime.js"></script>
<script type="text/javascript" src="/_layouts/15/sp.js"></script>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>
<script type="text/javascript">



$(document).ready(function() {

RestoreRecycleBin();
    
});


var itemsToRestore = [];
function RestoreRecycleBin() {
     
    var clientContext = new SP.ClientContext(_spPageContextInfo.webAbsoluteUrl);
    var web = clientContext.get_web();
    var recycleBinItemCollection = web.get_recycleBin();

    clientContext.load(recycleBinItemCollection);
   
    clientContext.executeQueryAsync(
    function() {
        var recycleBinItemEnumerator = recycleBinItemCollection.getEnumerator();
        while (recycleBinItemEnumerator.moveNext()) {
            var recycleBinItem = recycleBinItemEnumerator.get_current();
            
            if(recycleBinItem.get_title().toString().includes('.xml')){
                console.log('Recycle Bin Item Title: ' + recycleBinItem.get_title());
                 // Add the item to the array of items to restore
                    itemsToRestore.push(recycleBinItem);
                 // Restore the item from the Recycle Bin
                    //recycleBinItem.restore();
            }
           
        }
         // Now restore the items
         for(var i = 0; i < itemsToRestore.length; i++) {
            itemsToRestore[i].restore();
        }

        // Commit the restore operation
        clientContext.executeQueryAsync(
            function() {
                console.log('Items restored successfully.');
            }, 
            function(sender, args) {
                console.log('Failed to restore items. Error:' + args.get_message());
            }
        );
    }, 
    function(sender, args) {
        console.log('Failed to get recycle bin items. Error:' + args.get_message());
    });
}



</script>
