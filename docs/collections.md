# Collections in the OneDrive SDK for Android
The OneDrive SDK for Android allows you to work with item collections in OneDrive.

Collection information is contained within a `BaseCollectionPage` object, to which there are three core aspects:
* `getCurrentPage` The list of items within the current page.
* `getNextPage` The next page request builder. `null` if there are no additional pages.
* `getRawObject` The json that represents this page response, containing additional data.

## Getting a children collection by expansion
There are several navigation properties within the OneDrive service which can be expanded and returned in collections pages.  In order to access these properties, the service request needs to call `expand(...)` once the request is issued.

#### Parameters

|Name|Description|
|----|-----------|
|_itemId_|The item id of the item that has the children.|
|_propertyToExpand_|The name of the navigation property to expand.|
|_callback_|The callback when the get call is returned.|

```java
final String itemId = "0000000000000000000000";
final String propertyToExpand = "children";
final ICallback<Item> callback = new ICallback<Item> {
    @Override
    public void success(final Item result) {
        final IItemCollectionPage page = result.children;
        Toast.makeText(getActivity(), "Got children", Toast.LENGTH_LONG).show();
    }
    ...
    // Handle failure
}

oneDriveClient
    .getDrive()
    .getItems(itemId)
    .buildRequest()
    .expand(propertyToExpand)
    .get(callback);
```

## Getting a children collection by request

You can reference navigation properties directly by using the `getChildren()` function within the item's request builder.

#### Parameters

|Name|Description|
|----|-----------|
|_itemId_|The item id of the item that has the children.|
|_callback_|The callback when the get call is returned.|

```java
final String itemId = "0000000000000000000000";
final ICallback<IItemCollectionPage> callback = new ICallback<IItemCollectionPage> {
    @Override
    public void success(final IItemCollectionPage result) {
        Toast.makeText(getActivity(), "Got children", Toast.LENGTH_LONG).show();
    }
    ...
    // Handle failure
}

oneDriveClient
    .getDrive()
    .getItems(itemId)
    .getChildren()
    .buildRequest()
    .get(callback);
```

## Getting the next page
When getting a collection response, the `getNextPage()` function will return the null if there are no more pages, or the request builder to get the next page.  By using these request builders you can retrieve all of the items under a collection no matter the size.

|Name|Description|
|----|-----------|
|**itemId**|The item id of the item that has the children|
|**callback**|The callback when the get call is returned|


```java
final String itemId = "0000000000000000000000";
final ICallback<IItemCollectionPage> callback = new ICallback<IItemCollectionPage> {
    @Override
    public void success(final IItemCollectionPage result) {
        // If there was more pages retrieve them too
        if (result.getNextPage() != null) {
            result.getNextPage()
                  .buildRequest()
                  .get(new ICallback<IItemCollectionPage> ...);
        }
        Toast.makeText(getActivity(), "Got children", Toast.LENGTH_LONG).show();
    }
    ...
    // Handle failure
}

oneDriveClient
    .getDrive()
    .getItems(itemId)
    .getChildren()
    .buildRequest()
    .get(callback);
```

## Create a folder

You can create a folder within an item collection by using the request builders `getDrive`, `getItems`, and `getChildren` with the `create` method.

#### Parameters

|Name|Description|
|----|-----------|
|_folderToCreate_|The folder to create.|
|_parentId_|The item id for the parent item.|
|_callback_|The callback when the folder has been created.|

```java
final String parentId = "0000000000000000000000";
final Item folderToCreate = new Item();
folderToCreate.name = "NewFolder";
folderToCreate.folder = new Folder();

final ICallback<Item> callback = new ICallback<Item> {
    @Override
    public void success(final Item result) {
        Toast.makeText(getActivity(), "Created Folder", Toast.LENGTH_LONG).show();
    }
    ...
    // Handle failure
}

oneDriveClient
    .getDrive()
    .getItems(parentId)
    .getChildren()
    .buildRequest()
    .create(folderToCreate, callback);
```
