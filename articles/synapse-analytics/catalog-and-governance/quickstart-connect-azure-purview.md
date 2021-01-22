---
title: Azure Purview-fiók csatlakoztatása 
description: Egy Azure-beli hatáskörébe tartozó fiók összekötése egy szinapszis-munkaterülettel.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: cb6a63e4ead45d7751d1d3442793478cfdcd71c0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676340"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Rövid útmutató: Azure-beli hatáskörébe tartozó fiók összekötése egy szinapszis-munkaterülettel 


Ebben a rövid útmutatóban egy Azure-beli hatáskörébe tartozó fiókot fog regisztrálni egy szinapszis-munkaterületre. Ez a kapcsolat lehetővé teszi az Azure hatáskörébe tartozó eszközök felderítését, és a szinapszis-képességekkel való kommunikációt. 

A következő feladatokat végezheti el a Szinapszisban: 
- A felső keresőmező használatával megkeresheti a hatáskörébe tartozó eszközöket kulcsszavak alapján 
- Az adatok megértése metaadatok, Lineage, jegyzetek alapján 
- Az adatokat a munkaterülethez társított szolgáltatásokkal vagy integrációs adatkészletekkel csatlakoztathatja 
- Az adatkészletek elemzése szinapszis Apache Spark, szinapszis SQL és adatfolyam 

## <a name="prerequisites"></a>Előfeltételek 
- [Azure-beli hatáskörébe tartozó fiók](../../purview/create-catalog-portal.md) 
- [Szinapszis-munkaterület](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Bejelentkezés egy szinapszis-munkaterületre 

Lépjen a  [https://web.azuresynapse.net](https://web.azuresynapse.net) munkaterületre, és jelentkezzen be. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Azure-beli hatáskörébe tartozó fiók csatlakoztatásához szükséges engedélyek 

- Ahhoz, hogy egy Azure-beli hatáskörébe tartozó fiókot egy szinapszis munkaterülethez lehessen kapcsolni, Azure Portal IAM-ban **közreműködői** szerepkörre van szüksége a szinapszis munkaterületen, és hozzá kell férnie az Azure hatáskörébe tartozó fiókhoz.

## <a name="connect-an-azure-purview-account"></a>Azure Purview-fiók csatlakoztatása  

- A szinapszis munkaterületen válassza az Azure- **felügyelet kezelése** lehetőséget  ->  . Válassza **a Kapcsolódás a hatáskörébe fiókhoz** lehetőséget. 
- Kiválaszthatja **Az Azure-előfizetést** , vagy **manuálisan is megadhatja**. Az **Azure-előfizetésből** kiválaszthatja azt a fiókot, amelyhez hozzá tud férni. 
- A csatlakozás után látnia kell a hatáskörébe tartozó fiók nevét az **Azure hatáskörébe tartozó fiókban**. 
- Az adatkereséshez használhatja a szinapszis munkaterület felső középső sávján található keresési sávot. 

## <a name="nextsteps"></a>További lépések 

[Azure szinapszis-eszközök regisztrálása és vizsgálata az Azure hatáskörébe](../../purview/register-scan-azure-synapse-analytics.md)

[Adatok felfedezése, csatlakoztatása és feltárása Synapse-ban az Azure Purview segítségével](how-to-discover-connect-analyze-azure-purview.md)   
