---
title: 'Az Azure Analysis Services oktatóanyaga – 13. lecke: Üzembe helyezés | Microsoft Docs'
description: A lecke az oktatóanyag projektjének az Azure Analysis Services szolgáltatásban való üzembe helyezését ismerteti.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 889cc4224e2ffe31e15590c04c3dbf4b656bc386
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="deploy"></a>Üzembe helyezés

Ebben a leckében az üzembehelyezési tulajdonságokat fogja konfigurálni: megad egy Azure Analysis Services-kiszolgálót, amelyen az üzembe helyezést végzi, majd elnevezi a modellt. Ezután üzembe helyezi a modellt az adott példányon. Miután a modell üzembe lett helyezve, a felhasználók egy jelentéskészítő ügyfélalkalmazás segítségével csatlakozhatnak hozzá. További információkért lásd [az Azure Analysis Servicesben történő üzembe helyezést](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy) ismertető cikket.  
  
A lecke elvégzésének várható időtartama: **5 perc**  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a cikk a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. A jelen leckében található feladatok végrehajtása előtt be kell fejeznie az előző leckét ([12. lecke: Elemzés az Excelben](../tutorials/aas-lesson-12-analyze-in-excel.md)).  

> [!IMPORTANT]  
> Ahhoz, hogy üzembe helyezést végezhessen a távoli Analysis Services-kiszolgálón, [rendszergazdai jogosultságokkal](../analysis-services-server-admins.md) kell rendelkeznie.  

> [!IMPORTANT]  
> Ha egy helyszíni SQL Server kiszolgálóra telepítette az AdventureWorksDW2014 mintaadatbázist, és egy Azure Analysis Services-kiszolgálón helyezi üzembe a modellt, akkor szükség van egy [helyszíni adatátjáróra](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>A modell üzembe helyezése  
  
#### <a name="to-configure-deployment-properties"></a>Az üzembehelyezési tulajdonságok konfigurálása  

  
1.  A **Megoldáskezelő** területén kattintson a jobb gombbal az **AW Internet Sales** projektre, majd kattintson a **Tulajdonságok** elemre.  
  
2.  Az **AW Internet Sales tulajdonságlapjainak** párbeszédpanelének **Központi telepítési kiszolgáló** területén a **Kiszolgáló** tulajdonság alatt adja meg a teljes kiszolgálót.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  Az **Adatbázis** tulajdonságnál írja be az **Adventure Works Internet Sales** nevet.  
  
4.  A **Modell neve** tulajdonságnál írja be az **Adventure Works Internet Sales Model** nevet.  
  
5.  Ellenőrizze a beállításokat, majd kattintson az **OK** gombra.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Az Adventure Works internetes értékesítési modell központi telepítése
  
1.  A **Megoldáskezelő** területén kattintson a jobb gombbal az **AW Internet Sales** projektre, majd kattintson a **Létrehozás** parancsra.  

2.  Kattintson a jobb gombbal az **AW Internet Sales** projektre, majd kattintson az **Üzembe helyezés** parancsra.

    Ha az üzembe helyezést az Azure Analysis Services szolgáltatásban végzi, a rendszer felkérheti, hogy adja meg a fiókja adatait. Adja meg céges fiókját és jelszavát, például: nancy@adventureworks.com. Ennek a fióknak a kiszolgáló Rendszergazdák csoportjába kell tartoznia.
  
    Megjelenik az Üzembe helyezés párbeszédpanel, amelyen a modell metaadatainak és egyes tábláinak üzembehelyezési állapota látható.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. Miután az üzembe helyezés sikeresen befejeződött, nyugodtan kattintson a **Bezárás** gombra.  
  

Ez a lecke a táblázatos modell az SSDT-ből történő üzembe helyezésének leggyakoribb és legegyszerűbb módszerét ismerteti. A speciális üzembehelyezési lehetőségek, például az üzembehelyezési varázsló, illetve az XMLA és AMO használatával végzett automatizálás nagyobb rugalmasságot és egységességet kínálnak, valamint lehetővé teszik az üzembe helyezések ütemezését. További tudnivalók: [Táblázatosmodell-megoldások üzembe helyezése](https://docs.microsoft.com/sql/analysis-services/tabular-models/tabular-model-solution-deployment-ssas-tabular).

## <a name="conclusion"></a>Összegzés  
Gratulálunk! Végzett az első Analysis Services-beli táblázatos modellje elkészítésével és üzembe helyezésével. Ez az oktatóanyag végigvezette a táblázatos modellek létrehozásával kapcsolatos leggyakoribb feladatokon. Most, hogy az Adventure Works internetes értékesítési modell üzembe lett helyezve, az SQL Server Management Studio segítségével felügyelheti azt – létrehozhat feldolgozási szkripteket és egy biztonsági mentési tervet. Most már a felhasználók is kapcsolódhatnak a modellhez jelentéskészítő ügyfélalkalmazások útján (pl.: Microsoft Excel vagy Power BI).  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>A következő lépések
[Kapcsolódás Power BI Desktoppal](../analysis-services-connect-pbi.md)   
[Kiegészítő lecke – Dinamikus biztonság](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Kiegészítő lecke – Részletsorok](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Kiegészítő lecke – Hézagos hierarchiák](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
