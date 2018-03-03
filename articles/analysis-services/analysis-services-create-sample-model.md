---
title: "Adja hozzá az Azure Analysis Services-kiszolgáló egy minta táblázatos modell |} Microsoft Docs"
description: "Útmutató: a minta modell hozzáadása az Azure Analysis Servicesben."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/28/2018
ms.author: owend
ms.openlocfilehash: df83f5dd86d1edf857378ae69b16a86b57f9a2fe
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-add-a-sample-model"></a>Oktatóanyag: A minta modell hozzáadása

Ebben az oktatóanyagban ad hozzá egy minta Adventure Works modell a kiszolgálóhoz. A minta modell modellezési oktatóanyag az Adventure Works Internet értékesítési (1200-as) adatok befejezett verziója telepítve. Egy minta modell akkor hasznos, a modell kezelése teszteléshez, eszközök és az ügyfélalkalmazások csatlakoznak, és a modell-adatok lekérdezése.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Egy Azure Analysis Services-kiszolgálóhoz
- Kiszolgáló-rendszergazdai engedélyek

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-sample-model"></a>A minta modell létrehozása

1. A kiszolgáló **áttekintése**, kattintson a **új modell**.

    ![A minta modell létrehozása](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. A **új modell** > **válassza ki a datasource**, győződjön meg arról **az adatokat** van kiválasztva, és kattintson **Hozzáadás**.

    ![Válassza ki a mintaadatokat](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. A **áttekintése**, ellenőrizze a `adventureworks` minta jön létre.

    ![Válassza ki a mintaadatokat](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A minta modell által használt a gyorsítótár-memória-erőforrások. Ha nem használ a minta modell teszteléséhez, a kiszolgáló kell eltávolítani.

> [!NOTE]
> A lépések azt ismertetik, hogyan modell törlése egy kiszolgálóról szolgáltatáshoz az SSMS használatával. A modell a Tervező webszolgáltatáshoz előzetes is törölheti.

1. Az SSMS > **Object Explorer**, kattintson a **Connect** > **Analysis Services**.

2. A **kapcsolódás a kiszolgálóhoz**, illessze be a kiszolgáló nevét, majd a **hitelesítési**, válassza a **Active Directory - MFA-támogatással rendelkező univerzális**, megadja a felhasználónevét, és kattintson **Csatlakozás**.

    ![Bejelentkezés](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. A **Object Explorer**, kattintson a jobb gombbal a `adventureworks` adatbázis mintát, és kattintson a **törlése**.

    ![A minta-adatbázis törlése](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>További lépések 

[Csatlakozás a Power BI Desktop](analysis-services-connect-pbi.md)   
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)


