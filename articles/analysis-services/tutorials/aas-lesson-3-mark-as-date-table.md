---
title: 'Azure Analysis Services oktatóanyag – 3. lecke: Megjelölés dátumtáblaként | Microsoft Docs'
description: A lecke a dátumtáblák megjelölését ismerteti az Azure Analysis Services oktatóprojektjében.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 837543b6e9af714189e1d79eef483caabc2d32ad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597314"
---
# <a name="mark-as-date-table"></a>Megjelölés dátumtáblázatként

A 2. leckében (Az adatok beszerzése) importálta a DimDate nevű táblát. Ha a modellben ennek a táblának DimDate a neve, *Dátumtábla* néven is szokás nevezni, mivel ez tartalmazza a dátum- és időadatokat.  
  
A DAX időintelligenciára vonatkozó funkcióinak használatakor, például ha a mértékegységeket később hozza létre, meg kell határoznia egy *Dátumtáblát* és egy egyedi azonosító *Dátumoszlopot* a táblában.
  
Ebben a leckében megjelöli a DimDate táblát *Dátumtáblaként* és a Date oszlopot (a Dátumtáblában) *Dátumoszlopként* (egyedi azonosító).  

Mielőtt megjelölné a dátumtáblát és dátumoszlopot, érdemes lehet rendbe tenni a modellt, hogy könnyebben értelmezhető legyen. A DimDate táblában figyelje meg a **FullDateAlternateKey** elnevezésű oszlopot. Ez az oszlop egy-egy sort tartalmaz a táblában lévő mindegyik naptári naphoz. Ezt az oszlopot sokat fogja használni a mértékegységképletekben és jelentésekben. A FullDateAlternateKey elnevezés azonban nem túl jól azonosítja az oszlop funkcióját. Ha átkereszteli **Date** névre, könnyebb lesz azonosítania és felhasználnia a képletekben. Amikor lehetséges, érdemes átneveznie a különféle objektumokat, azaz a táblákat és oszlopokat, hogy könnyebben azonosíthatók legyenek az SSDT-ben és az ügyfelek jelentési rendszereiben, például a Power BI-ba és az Excelben. 
  
A lecke elvégzésének várható időtartama: **3 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a témakör a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. A leckében foglalt feladatok végrehajtása előtt el kell végeznie az előző leckét ([2. lecke: Az adatok beszerzése](../tutorials/aas-lesson-2-get-data.md)). 

### <a name="to-rename-the-fulldatealternatekey-column"></a>A FullDateAlternateKey oszlop átnevezése

1.  A modelltervezőben kattintson a **DimDate** táblára.

2.  Kattintson duplán a **FullDateAlternateKey** oszlop fejlécére, majd nevezze át **Date** névre.

  
### <a name="to-set-mark-as-date-table"></a>A Megjelölés dátumtáblaként beállítás megadása  
  
1.  Válassza ki a **Dátum** oszlopot, majd a **Tulajdonságok** ablakban az **Adattípus** alatt bizonyosodjon meg róla, hogy a **Dátum** lehetőség van kiválasztva.  
  
2.  Kattintson a **Tábla** menüre, majd a **Dátum**, és végül a **Megjelölés dátumtáblaként** lehetőségre.  
  
3.  A **Megjelölés dátumtáblaként** párbeszédpanelen, a **Dátum** listában válassza a **Date** oszlopot egyedi azonosítóként. Alapértelmezés szerint általában ez van kiválasztva. Kattintson az **OK** gombra. 

    ![aas-lesson3-date-table](../tutorials/media/aas-lesson3-date-table.png)
  

## <a name="whats-next"></a>A következő lépések
[4. lecke: Kapcsolatok létrehozása](../tutorials/aas-lesson-4-create-relationships.md)
  
