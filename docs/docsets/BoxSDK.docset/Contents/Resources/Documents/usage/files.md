Files
=====

File objects represent individual files in Box. They can be used to download a
file's contents, upload new versions, and perform other common file operations
(move, copy, delete, etc.).

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Get File Info](#get-file-info)
- [Update File](#update-file)
- [Upload File](#upload-file)
- [Upload New File Version](#upload-new-file-version)
- [Download File](#download-file)
- [Copy File](#copy-file)
- [Lock File](#lock-file)
- [Unlock File](#unlock-file)
- [Get File Thumbnail Image](#get-file-thumbnail-image)
- [Get File Embed Link](#get-file-embed-link)
- [Get File Collaborations](#get-file-collaborations)
- [Get File Comments](#get-file-comments)
- [Get File Tasks](#get-file-tasks)
- [Add File to Favorites](#add-file-to-favorites)
- [Remove File from Favorites](#remove-file-from-favorites)
- [Get Shared Link](#get-shared-link)
- [Set Shared Link](#set-shared-link)
- [Remove Shared Link](#remove-shared-link)
- [Get Representations](#get-representations)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Get File Info
-------------

To retrieve information about a file, call
[`client.files.get(fileId:fields:completion:)`][get-file]
with the ID of the file.  You can control which fields are returned on the resulting `File` object by passing the
desired field names in the optional `fields` parameter.

```swift
client.files.get(fileId: "11111", fields: ["name", "created_at"]) { (result: Result<File, BoxSDKError>) in
    guard case let .success(file) = result else {
        print("Error retrieving file information")
        return
    }

    print("File \(file.name) was created at \(file.createdAt)")
}
```

[get-file]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC11getFileInfo6fileId6fields10completionySS_SaySSGSgys6ResultOyAA0F0CAA0A5ErrorOGctF

Update File
-----------

To update a file record, call
[`client.files.updateFileInfo(fileId:name:description:parentId:sharedLink:tags:collections:lock:ifMatch:fields:completion:)`][update-file]
with the ID of the file to update and the properties to update.

```swift
client.files.update(fileId: "11111", name: "New file name.docx") { (result: Result<File, BoxSDKError>) in
    guard case let .success(file) = result else {
        print("Error updating file information")
        return
    }

    print("File \(file.name) was updated at \(file.modifiedAt)")
}
```


Upload File
-----------

To upload a file from data in memory, call
[`client.files.upload(data:name:parentId:progress:completion:)`][upload-file]
with the data to be uploaded, the name of the file, and the ID of the folder into which the file should be uploaded.
Use ID `"0"` to upload a file into the root folder, "All Files".

```swift
let data = "test content".data(using: .utf8)

client.files.upload(data: data, name: "Test File.txt", parentId: "0") { (result: Result<File, BoxSDKError>) in
    guard case let .success(file) = result else {
        print("Error uploading file")
        return
    }

    print("File \(file.name) was uploaded at \(file.createdAt) into \"\(file.parent.name)\"")
}
```

To upload a file from a stream, use
[`client.files.streamUpload(stream:fileSize:name:parentId:progress:completion:)`][upload-file-stream].

```swift
let data = "test content".data(using: .utf8)
let stream = InputStream(data: data)

client.files.streamUpload(
    stream: stream,
    fileSize: 12,
    name: "Test File.txt",
    parentId: "0"
) { (result: Result<File, BoxSDKError>) in
    guard case let .success(file) = result else {
        print("Error uploading file")
        return
    }

    print("File \(file.name) was uploaded at \(file.createdAt) into \"\(file.parent.name)\"")
}
```

[upload-file]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC10uploadFile4data4name8parentId8progress10completiony10Foundation4DataV_S2SySo10NSProgressCcys6ResultOyAA0F0CAA0A5ErrorOGctF
[upload-file-stream]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC16streamUploadFile0E08fileSize4name8parentId8progress10completionySo13NSInputStreamC_SiS2SySo10NSProgressCcys6ResultOyAA0G0CAA0A5ErrorOGctF

Upload New File Version
-----------------------

To upload a new version of an existing file, call
[`client.files.uploadVersion(forFile:name:contentModifiedAt:data:progress:completion:)`][upload-file-version]
with the ID of the file and the file contents to be uploaded.

```swift
let data = "updated file content".data(using: .utf8)
client.files.uploadVersion(
    forFile: "11111",
    name: "New file name.txt",
    contentModifiedAt: "2019-08-07T09:19:13-07:00",
    data: data
) { (result: Result<File, BoxSDKError>) in
    guard case let .success(file) = result else {
        print("Error uploading file version")
        return
    }

    print("New version of \(file.name) was uploaded")
}
```

[upload-file-version]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC17uploadFileVersion03forF04name17contentModifiedAt4data8progress10completionySS_S2S10Foundation4DataVySo10NSProgressCcys6ResultOyAA0F0CAA0A5ErrorOGctF

Download File
-------------

To download a file to the device, call
[`client.files.download(fileId:version:destinationURL:progress:completion:)`][download-file]
with the ID of the file to download and a URL to the location where the file should be downloaded to.

```swift
let url = FileManager.default.homeDirectoryForCurrentUser

client.files.download(fileId: "11111", destinationURL: url) { (result: Result<Void, BoxSDKError>) in
    guard case .success = result else {
        print("Error downloading file")
        return
    }

    print("File downloaded successfully")
}
```

[download-file]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC12downloadFile6fileId7version14destinationURL8progress10completionySS_SSSg10Foundation0K0VySo10NSProgressCcys6ResultOyytAA0A5ErrorOGctFhttp://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC12downloadFile6fileId7version14destinationURL8progress10completionySS_SSSg10Foundation0K0VySo10NSProgressCcys6ResultOyytAA0A5ErrorOGctF


Copy File
---------

To make a copy of a file, call
[`client.files.copy(fileId:parentId:name:version:fields:completion:)`][copy-file]
with the ID of the file to copy and the ID of the folder into which the copy should be placed.  In case an item with the
same name already exists in the destination folder, the `name` parameter can be used to provide a new name for the
copied file.  You can copy a specific file version by passing the version ID in the `version` parameter.

```swift
client.files.copy(fileId: "11111", parentId: "0") { (result: Result<File, BoxSDKError>) in
    guard case let .success(copiedFile) = result else {
        print("Error copying file")
        return
    }

    print("Copied file \(copiedFile.name) into folder \(copiedFile.parent.name); copy has file ID \(copiedFile.id)")
}
```

[copy-file]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC8copyFile6fileId06parentH04name7version6fields10completionySS_S2SSgAKSaySSGSgys6ResultOyAA0F0CAA0A5ErrorOGctFhttp://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC8copyFile6fileId06parentH04name7version6fields10completionySS_S2SSgAKSaySSGSgys6ResultOyAA0F0CAA0A5ErrorOGctF

Lock File
---------

To lock a file and prevent others from modifying it, call
[`client.files.lock(fileId:expiresAt:isDownloadPrevented:fields:completion:)`][lock-file]
with the ID of the file.  To also prevent others from downloading the file while it is locked, set the
`isDownloadPrevented` parameter to `true`.

```swift
client.files.lock(fileId: "11111") { (result: Result<File, BoxSDKError>) in
    guard case let .success(file) = result else {
        print("Error locking file")
        return
    }

    print("File \(file.name) locked")
}
```

[lock-file]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC8lockFile6fileId9expiresAt19isDownloadPrevented6fields10completionySS_SSSgSbSgSaySSGSgys6ResultOyAA0F0CAA0A5ErrorOGctF

Unlock File
-----------

To unlock a file, call [`client.files.unlock(fileId:fields:completion:)][unlock-file] with the ID of the file.

```swift
client.files.unlock(fileId: "11111") { (result: Result<File, BoxSDKError>) in
    guard case let .success(file) = result else {
        print("Error unlocking file")
        return
    }

    print("File \(file.name) unlocked")
}
```

[unlock-file]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC10unlockFile6fileId9expiresAt19isDownloadPrevented6fields10completionySS_SSSgSbSgSaySSGSgys6ResultOyAA0F0CAA0A5ErrorOGctF

Get File Thumbnail Image
------------------------

To retrieve the thumbnail image for a file, call
[`client.files.getThumbnail(forFile:extension:minHeight:minWidth:maxHeight:maxWidth:completion:)`][get-thumbnail]
with the ID of the file and the desired image format extension.  Optionally, constraints on the image
dimensions can be specified in the `minHeight`, `minWidth`, `maxHeight`, and `maxWidth` parameters.

```swift
client.files.getThumbnail(forFile: "11111", extension: .png) { (result: Result<Data, BoxSDKError>) in
    guard case let .success(thumbnailData) = result else {
        print("Error getting file thumbnail")
        return
    }

    let thumbnailImage = UIImage(data: thumbnailData)
}
```

[get-thumbnail]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC12getThumbnail7forFile9extension9minHeight0J5Width03maxK00mL010completionySS_AA0F9ExtensionOSiSgA3Nys6ResultOy10Foundation4DataVAA0A5ErrorOGctF

Get File Embed Link
-------------------

To retrieve a URL that can be embedded in a web page `<iframe>` to display a file preview, call
[`client.files.getEmbedLink(forFile:completion:)`][get-embed-link]
with the ID of the file.

```swift
client.files.getEmbedLink(forFile: "11111") { (result: Result<ExpiringEmbedLink, BoxSDKError>) in
    guard case let .success(embedLink) = result else {
        print("Error generating file embed link")
        return
    }

    print("File embed URL is \(embedLink.url)")
}
```

[get-embed-link]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC12getEmbedLink7forFile10completionySS_ys6ResultOyAA08ExpiringfG0CAA0A5ErrorOGctF

Get File Collaborations
-----------------------

To retrieve a list of collaborations on a file, call
[`client.files.listCollaborations(forFile:marker:limit:fields:)`][get-collaborations]
with the ID of the file.  This method returns an iterator in the completion, which is used to retrieve file collaborations.

```swift
client.files.listCollaborations(forFile: "11111") { result in
    switch results {
    case let .success(iterator):
        for i in 1 ... 10 {
            iterator.next { result in
                switch result {
                case let .success(collaboration):
                    print("Collaboration created by \(collaboration.createdBy?.name)")
                case let .failure(error):
                    print(error)
                }
            }
        }
    case let .failure(error):
        print(error)
    }
}
```

[get-collaborations]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC21getFileCollaborations03forF06marker5limit6fieldsAA18PaginationIteratorCyAA13CollaborationCGSS_SSSgSiSgSaySSGSgtF

Get File Comments
-----------------

To retrieve a list of comments on the file, call
[`client.files.listComments(forFile:offset:limit:fields:)`][get-comments]
with the ID of the file.  This method returns an iterator in the completion, which is used to page through the collection
of file comments.

```swift
client.files.listComments(forFile: "11111"){ results in
    switch results {
    case let .success(iterator):
        for i in 1 ... 10 {
            iterator.next { result in
                switch result {
                case let .success(comment):
                    print(comment.message)
                case let .failure(error):
                    print(error)
                }
            }
        }
    case let .failure(error):
        print(error)
    }
}
```

[get-comments]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC15getFileComments03forF06offset5limit6fieldsAA18PaginationIteratorCyAA7CommentCGSS_SiSgANSaySSGSgtF

Get File Tasks
--------------

To retrieve a list of file tasks, call [`client.files.listTasks(forFile:fields:)`][get-tasks] with the ID of the
file.  This method returns an iterator in the completion, which is used to retrieve file comments.

```swift
client.files.listTasks(forFile: "11111") { results in
    switch results {
    case let .success(iterator):
        for i in 1 ... 10 {
            iterator.next { result in
                switch result {
                case let .success(item):
                    print("Task messsage: \(task.message)")
                case let .failure(error):
                    print(error)
                }
            }
        }
    case let .failure(error):
        print(error)
    }
}
```

[get-tasks]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC12getFileTasks03forF06fieldsAA18PaginationIteratorCyAA4TaskCGSS_SaySSGSgtF

Add File to Favorites
---------------------

To add a file to the user's favorites collection, call
[`client.files.addToFavorites(fileId:completion:)`][add-to-favorites]
with the ID of the file.

```swift
client.files.addToFavorites(fileId: "11111") { (result: Result<Void, BoxSDKError>) in
    guard case .success = result else {
        print("Error adding file to favorites")
        return
    }

    print("File added to favorites")
}
```

[add-to-favorites]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC18addFileToFavorites6fileId10completionySS_ys6ResultOyytAA0A5ErrorOGctF

Remove File from Favorites
--------------------------

To remove a file from the user's favorites collection, call
[`client.files.removeFromFavorites(fileId:completion:)`][remove-from-favorites]
with the ID of the file.

```swift
client.files.removeFromFavorites(fileId: "11111") { (result: Result<Void, BoxSDKError>) in
    guard case .success = result else {
        print("Error removing file from favorites")
        return
    }

    print("File removed from favorites")
}
```

[remove-from-favorites]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC23removeFileFromFavorites6fileId10completionySS_ys6ResultOyytAA0A5ErrorOGctF

Get Shared Link
---------------

To retrieve the shared link associated with a file, call
[`client.files.getSharedLink(forFile:completion:)`][get-shared-link]
with the ID of the file.

```swift
client.files.getSharedLink(forFile: "11111") { (result: Result<SharedLink, BoxSDKError>) in
    guard case let .success(sharedLink) = result else {
        print("Error retrieving file shared link")
        return
    }

    print("File shared link URL is \(sharedLink.url), with \(sharedLink.access) access")
}
```

[get-shared-link]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC13getSharedLink7forFile10completionySS_ys6ResultOyAA0fG0CAA0A5ErrorOGctF

Set Shared Link
---------------

To add or update the shared link for a file, call
[`client.files.setSharedLink(forFile:unsharedAt:access:password:canDownload:completion:)][set-shared-link]
with the ID of the file and the shared link properties to set.

```swift
client.files.setSharedLink(forFile: "11111", access: .open) { (result: Result<SharedLink, BoxSDKError>) in
    guard case let .success(sharedLink) = result else {
        print("Error setting file shared link")
        return
    }

    print("File shared link URL is \(sharedLink.url), with \(sharedLink.access) access")
}
```

[set-shared-link]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC13setSharedLink7forFile13stopSharingAt6access8password11canDownload10completionySS_10Foundation4DateVSgAA0fG6AccessOSgAA17OptionalParameterOySSGSgSbSgys6ResultOyAA0fG0CAA0A5ErrorOGctF

Remove Shared Link
------------------

To remove a file's shared link, call
[`client.files.deleteSharedLink(forFile:completion:)`][delete-shared-link]
with the ID of the file.

```swift
client.files.deleteSharedLink(fileId: "11111") { (result: Result<Void, BoxSDKError>) in
    guard case .success = result else {
        print("Error removing file shared link")
        return
    }

    print("File shared link removed")
}
```

[delete-shared-link]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC16deleteSharedLink7forFile10completionySS_ys6ResultOyytAA0A5ErrorOGctF

Get Representations
-------------------

To retrieve information about available preview representations for a file, call
[`client.files.getRepresentations(fileId:xRepHints:completion:)`][get-representations]
with the ID of the file.  Omitting the `xRepHints` parameter will provide summary information about
all available representations; in order to retrieve the representation status and URL, the `xRepHints` parameter
must be passed to select the desired representations.

```swift
// Get full information about PDF representation
client.files.getRepresentations(
    fileId: "11111",
    xRepHints: "[pdf]"
) { (result: Result<[FileRepresentation], BoxSDKError>) in
    guard case let .success(representations) = result else {
        print("Error fetching representations")
        return
    }

    print("PDF representation generation status: \(representations[0]?.status?.state)")
}
```

[get-representations]: http://opensource.box.com/box-ios-sdk/Classes/FilesModule.html#/s:6BoxSDK11FilesModuleC18getRepresentations6fileId9xRepHints10completionySS_SSSgys6ResultOySayAA4FileC14RepresentationVGAA0A5ErrorOGctF
