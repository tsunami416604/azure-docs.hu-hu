---
title: "Adja hozzá az Azure Storage szolgáltatások csatlakoztatva a Visual Studio használatával |} Microsoft Docs"
description: "Azure Storage hozzáadása az alkalmazáshoz a Visual Studio kapcsolódó szolgáltatások hozzáadása párbeszédpanelen"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 521ec044-ad4b-4828-8864-01decde2e758
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2017
ms.author: kraigb
ms.openlocfilehash: 35638083cd75e1b751d00a9c8163a3bc7480f0cd
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Az Azure storage hozzáadása a Visual Studio kapcsolódó szolgáltatások használatával
A Visual Studio kapcsolódás a következő Azure Storage használatával a **kapcsolódó szolgáltatások hozzáadása** párbeszédpanel:

- C#-felhőszolgáltatás
- .NET-háttérrendszer mobilszolgáltatás
- ASP.NET-webhely vagy szolgáltatás
- Az ASP.NET Core szolgáltatás
- Azure webjobs-feladat szolgáltatás 

A csatlakoztatott szolgáltatás funkcióit a szükséges hivatkozásokat és a kapcsolat kód hozzáadása a projekthez, és megfelelően módosítja a konfigurációs fájlok. 

A művelet befejezését követően a **kapcsolódó szolgáltatások hozzáadása** párbeszédpanel automatikusan megjeleníti a dokumentációját, és részletesen leírja a dolgozni a blob-tároló, várólisták, szükséges lépéseket és táblázatot.

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Csatlakozás Azure Storage a kapcsolódó szolgáltatások párbeszédpanelen
1. Nyissa meg a projektet a Visual Studio

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **kapcsolódó szolgáltatások** csomópont, és a helyi menüt, majd válassza a **kapcsolódó szolgáltatás hozzáadása**.
   
    ![Adja hozzá az Azure szolgáltatás csatlakoztatva](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Az a **kapcsolódó szolgáltatások** lapon jelölje be **felhőalapú tárolás az Azure Storage**.
   
    ![Az Azure Storage hozzáadása](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. Az a **Azure Storage** párbeszédpanel, válasszon egy meglévő tárfiókot használ, válassza ki **Hozzáadás**.
   
    Ha létrehoz egy tárfiókot van szüksége, nyissa meg a következő lépéssel. Egyéb esetben folytassa a 6. lépés.
    
    ![Meglévő tárfiók hozzáadása a projekthez](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. A storage-fiók létrehozása: 
   
   1. Válassza ki **hozzon létre egy új Tárfiókot** a párbeszédpanel alján.

   1. Töltse ki a **Storage-fiók létrehozása** párbeszédpanel, válassza ki **létrehozása**.
      
       ![Új Azure storage-fiók](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. Ha a **Azure Storage** párbeszédpanel jelenik meg, az új tárfiók listájában jelenik meg. A listában jelölje ki az új tárfiókot, és válassza ki **Hozzáadás**.

1. A tárolási csatlakoztatott szolgáltatás alatt jelenik meg a **szolgáltatási hivatkozást lekérni** a projekt csomópontjára.
   
## <a name="how-your-project-is-modified"></a>A projekt módosítása hogyan
Ha befejezte a párbeszédpanelen, a Visual Studio hivatkozásokat ad, és módosítja a bizonyos konfigurációs fájlok. Az adott módosítások a projekt típusától függ: 

- ASP.NET-projekt - [Mi történt – ASP.NET-projektek](http://go.microsoft.com/fwlink/p/?LinkId=513126)
- Az ASP.NET Core projekt - [Mi történt – ASP.NET 5 projektek](http://go.microsoft.com/fwlink/p/?LinkId=513124) 
- Felhőszolgáltatás-projekt (webes és feldolgozói szerepkörök) - [Mi történt – Cloud Service projektek](http://go.microsoft.com/fwlink/p/?LinkId=516965)
- Webjobs-feladat projekt - [Mi történt – a webjobs-feladat projektek](visual-studio/vs-storage-webjobs-what-happened.md)

## <a name="next-steps"></a>Következő lépések
- [MSDN fórum: Az Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [A Microsoft Azure tárolás fejlesztői Blog](http://blogs.msdn.com/b/windowsazurestorage/)
- [Az Azure Storage-dokumentáció](https://docs.microsoft.com/azure/storage/)
