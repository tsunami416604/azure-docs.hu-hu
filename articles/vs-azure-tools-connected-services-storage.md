---
title: Adja hozzá az Azure Storage a Visual Studio csatlakoztatott szolgáltatásai segítségével |} A Microsoft Docs
description: Azure Storage hozzáadása az alkalmazáshoz a Visual Studio csatlakoztatott szolgáltatás hozzáadása párbeszédpanelen
services: visual-studio-online
author: ghogen
manager: douge
assetId: 521ec044-ad4b-4828-8864-01decde2e758
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/26/2017
ms.author: ghogen
ms.openlocfilehash: 031555659cc501365e762ba973c70b68129dbb4e
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969313"
---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Visual Studio csatlakoztatott szolgáltatásai segítségével az Azure storage hozzáadása
A Visual Studióval csatlakozhat a következő Azure Storage használatával a **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanel:

- C#-felhőszolgáltatás
- .NET-háttérrendszer mobilszolgáltatás
- ASP.NET-webhely vagy szolgáltatás
- ASP.NET Core-szolgáltatás
- Azure WebJob service 

A csatlakoztatott szolgáltatás funkciókat ad hozzá a szükséges hivatkozásokat és a kapcsolat kódot a projekthez, és megfelelően módosítja a konfigurációs fájlokat. 

A művelet befejezését követően a **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanel automatikusan megjeleníti a dokumentáció részletesen ismertetve a blob storage-üzenetsorok, való használatának megkezdéséhez szükséges lépéseket és a táblák.

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Kapcsolódás az Azure Storage segítségével a csatlakoztatott szolgáltatás párbeszédpanelen
1. Nyissa meg a projektjét a Visual Studióban

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **csatlakoztatott szolgáltatás** csomópontot, és a helyi menüt, és válassza a **csatlakoztatott szolgáltatás hozzáadása**.
   
    ![Adja hozzá az Azure csatlakoztatott szolgáltatás](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Az a **csatlakoztatott szolgáltatás** lapon jelölje be **felhőalapú tárolás az Azure Storage**.
   
    ![Az Azure Storage hozzáadása](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. Az a **Azure Storage** párbeszédpanel, válassza ki egy meglévő tárfiókot, és válassza ki **Hozzáadás**.
   
    Ha szeretne létrehozni egy storage-fiókot, nyissa meg a következő lépéssel. Egyéb esetben folytassa a 6. lépés.
    
    ![Meglévő tárfiók hozzáadása a projekthez](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. Tárfiók létrehozása: 
   
   1. Válassza ki **hozzon létre egy új Tárfiókot** a párbeszédpanel alján.

   1. Töltse ki a **Create Storage Account** párbeszédpanel, és válassza ki **létrehozás**.
      
       ![Új Azure storage-fiók](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. Ha a **Azure Storage** párbeszédpanel jelenik meg, az új tárfiók megjelenik a listában. Válassza ki a listából az új tárfiókot, és válassza ki **Hozzáadás**.

1. A tároló csatlakoztatott szolgáltatás megjelenik a **szolgáltatási hivatkozást** a projekt csomópontra.
   
## <a name="how-your-project-is-modified"></a>Hogyan módosul a projekthez
Ha befejezte a párbeszédpanelen, a Visual Studio hivatkozásokat ad, és módosítja a bizonyos konfigurációs fájlokat. Az egyes módosításokat a projekt típusától függ: 

- ASP.NET-projekt – [Mi történt – ASP.NET-projektek](http://go.microsoft.com/fwlink/p/?LinkId=513126)
- ASP.NET Core-projekt – [Mi történt – ASP.NET 5-projektek](http://go.microsoft.com/fwlink/p/?LinkId=513124) 
- Felhőszolgáltatás-projekt (webes és feldolgozói szerepkörök) – [Mi történt – a Cloud Service projektek](http://go.microsoft.com/fwlink/p/?LinkId=516965)
- Webjobs-feladat projekt - [Mi történt – a webjobs-feladat projektek](visual-studio/vs-storage-webjobs-what-happened.md)

## <a name="next-steps"></a>További lépések
- [MSDN-fórum: Az Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [A Microsoft Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/)
- [Az Azure Storage dokumentációja](https://docs.microsoft.com/azure/storage/)
