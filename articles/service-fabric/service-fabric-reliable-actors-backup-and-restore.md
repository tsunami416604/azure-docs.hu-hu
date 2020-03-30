---
title: Az Azure Service Fabric szereplőinek biztonsági mentése és visszaállítása
description: Ismerje meg, hogyan valósíthatja meg a biztonsági mentést és visszaállítást az Azure Service Fabric-szereplőkben.
author: vturecek
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vturecek
ms.openlocfilehash: 41ba3f9c7d362756b800005d0c140c23dd96caa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370459"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Megbízható szereplők biztonsági mentésének és visszaállításának megvalósítása

> [!NOTE]
> A Microsoft azt javasolja, hogy [rendszeres biztonsági mentési és visszaállítási](service-fabric-backuprestoreservice-quickstart-azurecluster.md) megbízható állapotalapú szolgáltatások és megbízható szereplők adatbiztonsági biztonsági mentésének konfigurálása. 
> 

A következő példában egy egyéni aktor szolgáltatás elérhetővé teszi a metódust az aktor adatok biztonsági másolatot készít, kihasználva a már jelen lévő, a szolgáltatásban `ActorService`már jelen lévő szolgáltatás ban szereplő szolgáltatás átirányító figyelőt:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```

Ebben a `IMyActorService` példában egy átirányító szerződés, `IService` amely megvalósítja `Service` (C#) és `MyActorService`(Java), majd végre . Hozzáadásával ez a remoting `IMyActorService` szerződés, módszerek most is elérhető az ügyfél `ActorServiceProxy`létrehozásával egy remoting proxy keresztül:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

A Megbízható szereplőkkel kapcsolatos további információkért olvassa el az alábbi cikkeket:
* [Szereplő állapotkezelése](service-fabric-reliable-actors-state-management.md)
* [A szereplő életciklusa és a szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md)
* [Actors API referenciadokumentáció](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
