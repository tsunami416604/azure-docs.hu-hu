---
title: Alakítsa át az XML-adatok átalakító - Azure Logic Apps |} Microsoft Docs
description: Hozzon létre átalakítások vagy mapps átalakítani az XML-adatok között a logic Apps alkalmazásokat a vállalati integrációs SDK használatával
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: fd59b6b3f51adb538e774bc5bb089880ca22e97e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="enterprise-integration-with-xml-transforms"></a>XML-átalakítók vállalati integrációja
## <a name="overview"></a>Áttekintés
A vállalati integrációs átalakító összekötő konvertálja az adatokat egy adott formátumból más formátumba. Például előfordulhat, hogy egy bejövő üzenet, amely tartalmazza az aktuális YearMonthDay formátumú dátumot. Átalakítás segítségével formázza újra a MonthDayYear formátumú dátum.

## <a name="what-does-a-transform-do"></a>Mire átalakító?
Átalakító, amely más néven a térkép, a forrás XML-séma (bemeneti) és a célként megadott XML-séma (kimenet) áll. Különféle beépített funkciók segítségével elősegítésére módosítására, vagy szabályozhatja az adatok karakterlánc-feldolgozás, feltételes hozzárendelés, aritmetikai kifejezésekben, dátum idő is beleértve, még akkor is ismétlési szerkezeteket.

## <a name="how-to-create-a-transform"></a>Hozzon létre egy átalakítási hogyan?
A Visual Studio használatával hozhat létre egy átalakítási/térkép [vállalati integrációs SDK](https://aka.ms/vsmapsandschemas). Ha elkészült, létrehozása és tesztelése a transzformáció bemeneti, töltse fel az átalakítás integrációs fiókjába. 

## <a name="how-to-use-a-transform"></a>Átalakítás használata
A átalakító térkép integrációs fiókjába való feltöltésekor használhatja a logikai alkalmazás létrehozása. A logikai alkalmazást fut a átalakítások, amikor elindul a logikai alkalmazás (és van-e bemeneti át kell alakítani).

**Az alábbiakban egy átalakítási használatának a lépéseit**:

### <a name="prerequisites"></a>Előfeltételek

* Integráció-fiók létrehozása, és a térkép felvétele  

Most, hogy az az Előfeltételek korábban hozott ítélt, ideje létrehozni a logikai alkalmazás:  

1. Logikai alkalmazás létrehozása és [hivatkozás integrációs fiókjába](../logic-apps/logic-apps-enterprise-integration-accounts.md "integrációs fiók csatolása logikai alkalmazás további") , amely tartalmazza a térképen.
2. Adja hozzá a **kérelem** eseményindító a logikai alkalmazáshoz  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Hozzáadás a **átalakítása XML** első kiválasztásával művelet **művelet hozzáadása**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Írja be a word *átalakítási* a keresési mezőbe, amely a használni kívánt a műveletek szűrése  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Válassza ki a **átalakítása XML** művelet   
6. Adja hozzá az XML-fájl **tartalom** , amely alakít át. Is használhatja, mint a HTTP-kérelem érkezik XML-adatokat a **tartalom**. Ebben a példában válassza ki a logikai alkalmazás kiváltó HTTP-kérelem törzsét.

   > [!NOTE]
   > Győződjön meg arról, hogy a tartalom a **átalakítása XML** XML formátumú-e. Ha a tartalom nincs a XML vagy base64-kódolású, meg kell adnia egy kifejezés, amely feldolgozza a tartalmat. Használhat például [funkciók](logic-apps-workflow-definition-language.md#functions), például ```@base64ToBinary``` az tartalom dekódolási vagy ```@xml``` az XML-tartalom feldolgozása.
 

7. Válassza ki a nevét a **térkép** , hogy szeretné-e az átalakítás elvégzéséhez. A térkép szerepelniük kell a integrációs fiókját. Egy korábbi lépésben már adott a logikai alkalmazás eléréséhez, amely tartalmazza a térképre integrációs fiókját.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Mentse a munkáját  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Ezen a ponton befejezte a térképre beállítása. Egy valós alkalmazás érdemes lehet az átalakított adatok tárolása egy LOB-alkalmazás, például SalesForce. Egyszerűen küldhet az átalakítás kimenetét Salesforce műveletet. 

Az átalakítási kérelmet a HTTP-végpont, így most tesztelheti.  


## <a name="features-and-use-cases"></a>Szolgáltatások és a használati esetek
* Lehet, hogy a térkép létrehozott átalakítása egyszerű, például másolása nevét és címét egy dokumentum másik. Vagy a out-of-az-box térkép műveletekkel összetettebb átalakítások is létrehozhat.  
* Több leképezés műveletek vagy funkciók azonnal elérhetők, beleértve a karakterláncok, dátum időpont függvényei, és így tovább.  
* A sémákban közötti közvetlen adatmásolás teheti meg. A leképező, az SDK tartalmazza ez az egy vonal, a forrás-séma elemei kapcsoló, mint a cél séma a lehető legegyszerűbb.  
* Olyan térképet létrehozásakor megtekintheti a térkép, amely megjeleníti azokat a kapcsolatokat és hivatkozásokat hoz létre grafikus ábrázolása.
* A teszt térkép szolgáltatás használatával adja hozzá a minta XML üzenet. Egyszerű kattintással a létrehozott leképezés tesztelése, és tekintse meg a létrehozott kimeneti.  
* Töltse fel a meglévő leképezéseket  
* Az XML-formátuma támogatását is magában foglalja.

## <a name="advanced-features"></a>Speciális funkciók

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referencia szerelvény, illetve a maps egyéni kód 
Az átalakítási műveletet is támogatja a maps vagy külső szerelvény hivatkozással alakítja. Ez a funkció lehetővé teszi, hogy a hívások egyéni .NET kódot közvetlenül az XSLT-leképezések. Az alábbiakban a maps-szerelvény használatának előfeltételei.

* A térkép és a térkép kell lennie a hivatkozott [tölt fel az integráció fiókba](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Térkép és szerelvény is fel kell tölteni, meghatározott sorrendben. A szerelvény kell tölteni, mielőtt feltölti a térkép, amely a szerelvényre hivatkozik.

* A térkép ezek az attribútumok és a szerelvény kód hívása tartalmazó CDATA szakasz kell rendelkeznie:

    * **név** az egyéni szerelvénynév.
    * **névtér** az a névtér, amely tartalmazza az egyéni kódot a szerelvényben.

  Ez a példa bemutatja, hogy olyan szerelvényre hivatkozik, neve "XslUtilitiesLib" és a hívások a `circumreference` metódus a szerelvényből.

  ````xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
     <circles>
        <xsl:for-each select="circle">
            <circle>
                <xsl:copy-of select="node()"/>
                    <circumference>
                        <xsl:value-of select="user:circumference(radius)"/>
                    </circumference>
            </circle>
        </xsl:for-each>
     </circles>
    </xsl:template>
    </xsl:stylesheet>
  ````


### <a name="byte-order-mark"></a>Bájt rendelés be van jelölve
Alapértelmezés szerint az átalakítás válaszát elindul a bájt rendelés jel (AJ). Ez a funkció csak a kód nézetre szerkesztőben végzett munka közben érheti el. Ez a funkció letiltásához adja meg a `disableByteOrderMark` a a `transformOptions` tulajdonság:

````json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
````





## <a name="learn-more"></a>Részletek
* [További tudnivalók a vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag")  
* [További információ a maps](../logic-apps/logic-apps-enterprise-integration-maps.md "vállalati integrációs maps ismertetése")  

