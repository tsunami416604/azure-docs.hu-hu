---
title: a.Net Core alkalmazás létrehozása és közzététele távoli Linux-fürtön
description: Távoli Linux-fürtöt célzó .Net Core alkalmazások létrehozása és közzététele a Visual Studio-ból
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614349"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Távoli Linux Service Fabric-fürtöt célzó .Net Core alkalmazások létrehozása és közzététele a Visual Studió segítségével
A Visual Studio eszközeivel fejlesztheti és közzéteheti a Service Fabric .Net Core alkalmazásokat, amelyek linuxos Service Fabric-fürtöt céloznak meg. Az SDK-verziónak 3.4-es vagy újabb verziónak kell lennie a .Net Core alkalmazás Visual Studio-ból származó Linux Service Fabric-fürtöket célzó telepítéséhez.

> [!Note]
> A Visual Studio nem támogatja a Linuxot célzó Service Fabric-alkalmazások hibakeresését.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Service Fabric-alkalmazás létrehozása .Net Core-t célozva
1. Indítsa el a Visual Studio **alkalmazást rendszergazdaként**.
2. Projekt létrehozása **a >Új >Projekttel.**
3. Az **Új projekt** párbeszédpanelen válassza a **Cloud -> Service Fabric application lehetőséget.**
![létrehozás-alkalmazás]
4. Nevezze el az alkalmazást, és kattintson **az Ok gombra.**
5. Az **Új szolgáltatásháló szolgáltatás** lapon válassza ki a **.Net Core szakasz**ban létrehozni kívánt szolgáltatás típusát.
![létrehozás-szolgáltatás]

## <a name="deploy-to-a-remote-linux-cluster"></a>Telepítés távoli Linux-fürtre
1. A megoldáskezelőben kattintson a jobb gombbal az alkalmazásra, és válassza a **Build**lehetőséget.
![build-alkalmazás]
2. Miután a build folyamat az alkalmazás befejeződött, jobb klikk a szolgáltatás, és válassza ki a szerkessze a **csproj fájlt**.
![edit-csproj]
3. Szerkessze az UpdateServiceFabricManifestEnabled tulajdonságot Igaz ról **Hamis** értékre, ha a szolgáltatás **aktor projekttípusa.** Ha az alkalmazás nem rendelkezik aktor szolgáltatással, ugorjon a 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Ha az UpdateServiceFabricManifestEnabled fájlbeállítást hamis értékre állítja, a build során letiltja a ServiceManifest.xml fájl frissítéseit. Az olyan módosítások, mint a szolgáltatás hozzáadása, eltávolítása vagy átnevezése, nem jelennek meg a ServiceManifest.xml fájlban. Ha bármilyen módosítás történik, vagy manuálisan kell frissítenie a ServiceManifest-et, vagy ideiglenesen igazra kell állítania az UpdateServiceFabricManifestEnabled értéket, és létre kell hoznia azt a szolgáltatást, amely frissíti a ServiceManifest.xml fájlt, majd visszaállítja hamisra.
>

4. Frissítse a RuntimeIndetifier-t win7-x64-ről a szolgáltatási projekt célplatformjára.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. A ServiceManifest programban frissítse a belépési pont programot az .exe eltávolításához. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. A Megoldáskezelőben kattintson a jobb gombbal az alkalmazásra, és válassza a **Közzététel parancsot.** Megjelenik a **Publish** (Közzététel) párbeszédpanel.
7. A **Kapcsolatvégpont ban**válassza ki a végpontot a távoli Service Fabric Linux-fürthöz, amelyet meg szeretne célozni.
![közzétételi alkalmazás]

<!--Image references-->
[létrehozás-alkalmazás]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[létrehozás-szolgáltatás]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-alkalmazás]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[közzétételi alkalmazás]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>További lépések
* További információ a [Service Fabric .Net Core szolgáltatással való ismerkedéséről](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
