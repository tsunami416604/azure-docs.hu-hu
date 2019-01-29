---
title: XML-formátum – Azure Logic Apps között átalakítás |} A Microsoft Docs
description: Átalakítások vagy XML átalakítása az Azure Logic Apps Enterprise Integration Pack-formátumok közötti leképezések létrehozása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.date: 07/08/2016
ms.openlocfilehash: 4ebd96613378bbd907beb5109343a2427b1300b0
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095668"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML-ből az Azure Logic Apps Enterprise Integration Pack-formátumok közötti-térképek létrehozása

Az Enterprise integration átalakító connector adatok egy adott formátumból más formátumba konvertálja. Például előfordulhat, hogy rendelkezik egy bejövő üzenet, amely tartalmazza az aktuális dátumot a YearMonthDay formátumban. Egy-egy átalakítási segítségével formázza a dátum a MonthDayYear formátumú lehet.

## <a name="what-does-a-transform-do"></a>Mire átalakító?
Olyan átalakítást, amely más néven egy térképet, egy forrás XML-séma (bemenet) és a egy cél XML-séma (kimenet) áll. Használhatja a különféle beépített funkciók módosítására, és szabályozhatja az adatok segítségével karakterlánc-feldolgozás, feltételes hozzárendelés, számtani kifejezéseket, dátum-idő formázót is beleértve, és akár hurkolás szerkezeteket.

## <a name="how-to-create-a-transform"></a>Hogyan hozhat létre egy-egy átalakítási?
A Visual Studio használatával is létrehozhat egy átalakítási térkép [vállalati integráció SDK](https://aka.ms/vsmapsandschemas). Amikor végzett, létrehozása és tesztelése a transzformáció, az az integrációs fiókba töltse fel az átalakítás. 

## <a name="how-to-use-a-transform"></a>Egy-egy átalakítási használata
Miután a átalakító térkép az integrációs fiókba töltse fel, hozzon létre egy logikai alkalmazást használhatja. A Logic app-futtatások az átalakításokat, amikor a logikai alkalmazás aktiválódik (és nincs a bemeneti tartalom átalakíthatók).

**A következő lépéseket egy-egy átalakítási használandó**:

### <a name="prerequisites"></a>Előfeltételek

* Integrációs fiók létrehozása és a egy térkép hozzáadása  

Most, hogy elvégzi, az előfeltételeket, azaz a logikai alkalmazás létrehozásához szükséges idő:  

1. Hozzon létre egy logikai alkalmazást, és [hivatkozás az integrációs fiókba](../logic-apps/logic-apps-enterprise-integration-accounts.md "Ismerkedjen meg az integrációs fiók összekapcsolása egy logikai alkalmazást") , amely tartalmazza a térképen.
2. Adjon hozzá egy **kérelem** eseményindítót a logikai alkalmazáshoz  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Hozzáadás a **átalakítása XML** első kiválasztásával művelet **művelet hozzáadása**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Adja meg a word *átalakítása* a keresőmezőbe, amely a használni kívánt a műveletek szűrése  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Válassza ki a **átalakítása XML** művelet   
6. Adja hozzá az XML-fájl **tartalom** , amely átalakítja. Használhat bármilyen XML-adatok, mint a HTTP-kérést kap a **tartalom**. Ebben a példában válassza ki, amely kiváltotta a logikai alkalmazás a HTTP-kérelem törzse.

   > [!NOTE]
   > Győződjön meg arról, hogy a tartalmát a **átalakítása XML** XML formátumú. Ha a tartalom nem szerepel XML vagy base64-kódolású, meg kell adnia egy kifejezés, amely feldolgozza a tartalmat. Használhatja például [funkciók](logic-apps-workflow-definition-language.md#functions), pl. ```@base64ToBinary``` tartalom visszafejtéséhez vagy ```@xml``` feldolgozására a tartalom XML-fájlként.
 

7. Válassza ki a nevét, a **térkép** , hogy szeretné-e az átalakítás végrehajtásához. A térkép már kell lennie az integrációs fiókban. Egy korábbi lépésben már megadott a logikai alkalmazás-hozzáférés az integrációs fiókba, amely tartalmazza a térképen.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Mentse a munkáját  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Ezen a ponton végzett a térkép beállítása. Egy valós alkalmazás esetében érdemes lehet, hogy az átalakított adatok tárolása egy ÜZLETÁGI alkalmazás, például a SalesForce. Könnyedén, egy olyan műveletet, megtekintheti az átalakítás kimenetét a Salesforce-hoz. 

Az átalakítás most tesztelheti a kérést, így a HTTP-végpontot.  


## <a name="features-and-use-cases"></a>Funkciók és alkalmazási helyzetek
* Lehet, hogy létrehozott egy térképen az átalakítás egyszerű, például a nevét és címét egy dokumentumot egy másik másol. Másik lehetőségként a térkép a-beépített műveletek használatával összetettebb átalakításokat is létrehozhat.  
* Több térkép műveleteket, illetve a funkciók azonnal elérhetők, beleértve a karakterláncokat, dátum-időpont függvényeivel, és így tovább.  
* A közvetlen adatok másolása a sémák között teheti meg. Az SDK-ban található Leképezőjét Ez az egyszerű: egy vonal, hogy a forrás-séma elemei összekapcsolja az a cél sémában megfelelőik.  
* A térkép létrehozásakor, megtekintheti a térkép, amelyen látható a kapcsolatok és a hivatkozásokat hoz létre grafikus ábrázolását.
* A vizsgálati térkép funkció használatával adjon meg egy minta XML-üzenetet. Egyetlen kattintással a létrehozott leképezés tesztelése, és tekintse meg a generált kimeneti.  
* Töltse fel a meglévő térképek  
* Az XML-formátum támogatását tartalmazza.

## <a name="advanced-features"></a>Speciális funkciók

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referencia-szerelvény vagy egyéni kód a térképek 
Az átalakítási műveletet is támogatja a maps vagy hivatkozó külső szerelvény alakítja át. Ez a funkció lehetővé teszi, hogy a hívások közvetlenül az XSLT-leképezések egyéni .NET-kódot. Az alábbiakban a maps-szerelvény használatának előfeltételei.

* A térkép és a a térkép kell lennie a hivatkozott szerelvényt [integrációs fiók feltöltött](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Térkép és szerelvény is szükséges, meghatározott sorrendben kell feltölteni. A szerelvény fel kell tölteni, mielőtt feltölti a térkép, amely a szerelvény hivatkozik.

* A térkép ezek az attribútumok és a hívást a szerelvény kódot tartalmazó CDATA szakasz kell rendelkeznie:

    * **név** egyéni szerelvény neve.
    * **névtér** az a névtér, amely tartalmazza az egyéni kódot a szerelvényben.

  Ez a példa bemutatja, hogy egy "XslUtilitiesLib" és a hívások nevű szerelvény hivatkozik a `circumreference` metódus a szerelvényből.

  ```xml
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
  ```


### <a name="byte-order-mark"></a>Bájtsorrendjelző
Alapértelmezés szerint az átalakítás válasza elindítja a bájt rendelés Mark (AJ). Ez a funkció csak a Kódnézet szerkesztőben használata során is elérheti. Ez a funkció letiltásához adja meg a `disableByteOrderMark` számára a `transformOptions` tulajdonság:

```json
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
```





## <a name="learn-more"></a>Részletek
* [További információ az Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")  
* [További tudnivalók a maps](../logic-apps/logic-apps-enterprise-integration-maps.md "megismerheti a vállalati integrációs térképek")  

