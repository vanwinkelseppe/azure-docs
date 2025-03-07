---
title: Delete and restore a blob with .NET - Azure Storage
description: Learn how to delete and restore a blob in your Azure Storage account using the .NET client library
services: storage
author: normesta

ms.author: normesta
ms.date: 03/28/2022
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.devlang: csharp, python
ms.custom: "devx-track-csharp, devx-track-python"
---

# Delete and restore a blob in your Azure Storage account using the .NET client library

This article shows how to delete blobs with the [Azure Storage client library for .NET](/dotnet/api/overview/azure/storage). If you've enabled blob soft delete, you can restore deleted blobs.

## Delete a blob

To delete a blob, call either of these methods:

- [Delete](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

The following example deletes a blob.

```csharp
public static async Task DeleteBlob(BlobClient blob)
{
    await blob.DeleteAsync();
}
```

## Restore a deleted blob

Blob soft delete protects an individual blob and its versions, snapshots, and metadata from accidental deletes or overwrites by maintaining the deleted data in the system for a specified period of time. During the retention period, you can restore the blob to its state at deletion. After the retention period has expired, the blob is permanently deleted. For more information about blob soft delete, see [Soft delete for blobs](soft-delete-blob-overview.md).

You can use the Azure Storage client libraries to restore a soft-deleted blob or snapshot. 

#### Restore soft-deleted objects when versioning is disabled

To restore deleted blobs when versioning is not enabled, call either of the following methods:

- [Undelete](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.undelete)
- [UndeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.undeleteasync)

These methods restore soft-deleted blobs and any deleted snapshots associated with them. Calling either of these methods for a blob that has not been deleted has no effect. The following example restores  all soft-deleted blobs and their snapshots in a container:

```csharp
public static async Task UnDeleteBlobs(BlobContainerClient container)
{
    foreach (BlobItem blob in container.GetBlobs(BlobTraits.None, BlobStates.Deleted))
    {
        await container.GetBlockBlobClient(blob.Name).UndeleteAsync();
    }
}
```

To restore a specific soft-deleted snapshot, first call the [Undelete](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.undelete) or [UndeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.undeleteasync) on the base blob, then copy the desired snapshot over the base blob. The following example restores a block blob to the most recently generated snapshot:

```csharp
public static async Task RestoreSnapshots(BlobContainerClient container, BlobClient blob)
{
    // Restore the deleted blob.
    await blob.UndeleteAsync();

    // List blobs in this container that match prefix.
    // Include snapshots in listing.
    Pageable<BlobItem> blobItems = container.GetBlobs
                    (BlobTraits.None, BlobStates.Snapshots, prefix: blob.Name);

    // Get the URI for the most recent snapshot.
    BlobUriBuilder blobSnapshotUri = new BlobUriBuilder(blob.Uri)
    {
        Snapshot = blobItems
                    .OrderByDescending(snapshot => snapshot.Snapshot)
                    .ElementAtOrDefault(1)?.Snapshot
    };

    // Restore the most recent snapshot by copying it to the blob.
    blob.StartCopyFromUri(blobSnapshotUri.ToUri());
}
```

#### Restore soft-deleted blobs when versioning is enabled

To restore a soft-deleted blob when versioning is enabled, copy a previous version over the base blob. You can use either of the following methods:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

```csharp
public static void RestoreBlobsWithVersioning(BlobContainerClient container, BlobClient blob)
{
    // List blobs in this container that match prefix.
    // Include versions in listing.
    Pageable<BlobItem> blobItems = container.GetBlobs
                    (BlobTraits.None, BlobStates.Version, prefix: blob.Name);

    // Get the URI for the most recent version.
    BlobUriBuilder blobVersionUri = new BlobUriBuilder(blob.Uri)
    {
        VersionId = blobItems
                    .OrderByDescending(version => version.VersionId)
                    .ElementAtOrDefault(1)?.VersionId
    };

    // Restore the most recently generated version by copying it to the base blob.
    blob.StartCopyFromUri(blobVersionUri.ToUri());
}
```

## See also

- [Get started with Azure Blob Storage and .NET](storage-blob-dotnet-get-started.md)
- [Delete Blob](/rest/api/storageservices/delete-blob) (REST API)
- [Soft delete for blobs](soft-delete-blob-overview.md)
- [Undelete Blob](//rest/api/storageservices/undelete-blob) (REST API)
