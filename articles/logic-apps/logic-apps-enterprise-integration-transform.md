---
title: XML átalakítása formátumok között
description: Az Azure Logic Apps enterprise integrációs csomaggal rendelkező, formátumai között XML-t konvertáló átalakítások vagy leképezések létrehozása
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 500769a39ba7658b35c1abb80101f6234170c941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792390"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Az Azure Logic Apps formátumai között formázó dns-t átalakító leképezések létrehozása az Enterprise Integration Pack csomaggal

A vállalati integrációs transzformátor összekötő az adatokat egyik formátumból a másikba konvertálja. Előfordulhat például, hogy egy bejövő üzenet tartalmazza az aktuális dátumot a YearMonthDay formátumban. Átalakítással újraformázhatja a MonthDayYear formátumban lévő dátumot.

## <a name="what-does-a-transform-do"></a>Mit csinál egy átalakítás?
Az átalakítás, amely et leképezésnek is neveznek, egy forrás XML-sémából (a bemenetből) és egy cél XML-sémából (a kimenetből) áll. Különböző beépített függvények segítségével kezelheti vagy szabályozhatja az adatokat, beleértve a karakterlánc-műveleteket, a feltételes hozzárendeléseket, a számtani kifejezéseket, a dátum időforitálási időt, sőt a konstruktorokat is.

## <a name="how-to-create-a-transform"></a>Hogyan hozzunk létre egy átalakulás?
Átalakítást/térképet a Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas)segítségével hozhat létre. Ha befejezte az átalakítás létrehozását és tesztelését, feltölti az átalakítást az integrációs fiókjába. 

## <a name="how-to-use-a-transform"></a>Hogyan kell használni egy átalakítás
Miután feltöltötte az átalakítás/térkép az integrációs fiókba, használhatja egy logikai alkalmazás létrehozásához. A logikai alkalmazás futtatja az átalakításokat, amikor a logikai alkalmazás aktiválódik (és van bemeneti tartalom, amelyet át kell alakítani).

**Itt vannak a lépéseket, hogy használja a transform:**

### <a name="prerequisites"></a>Előfeltételek

* Integrációs fiók létrehozása és térkép hozzáadása  

Most, hogy gondoskodott az előfeltételekről, itt az ideje, hogy létrehozza a logikai alkalmazást:  

1. Hozzon létre egy logikai alkalmazást, és csatolja a térképet tartalmazó [integrációs fiókhoz.](../logic-apps/logic-apps-enterprise-integration-accounts.md "Ismerje meg, hogyan lehet integrációs fiókot összekapcsolni egy logikai alkalmazással")
2. **Kérelem-eseményindító** hozzáadása a logikai alkalmazáshoz  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Az **XML átalakítása** művelet hozzáadása a **Művelet hozzáadása** lehetőség kiválasztásával   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Írja be az *átalakítás* szót a keresőmezőbe, hogy az összes műveletet a használni kívánt műveletre szűrje.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Az **XML átalakítása** művelet kijelölése   
6. Adja hozzá az átalakított **XML-tartalmat.** A HTTP-kérelemben kapott XML-adatokat **tartalomként használhatja.** Ebben a példában válassza ki a logic alkalmazást aktiváló HTTP-kérelem törzsét.

   > [!NOTE]
   > Győződjön meg arról, hogy az **XML átalakítása** xml tartalma XML. Ha a tartalom nem XML-ben vagy base64-kódolású, meg kell adnia egy kifejezést, amely feldolgozza a tartalmat. Használhat például [függvényeket,](logic-apps-workflow-definition-language.md#functions) ```@base64ToBinary``` például a ```@xml``` tartalom dekódolásához vagy a tartalom XML-ként történő feldolgozásához.
 

7. Válassza ki az átalakítás végrehajtásához használni kívánt **térkép** nevét. A térképnek már az integrációs fiókban kell lennie. Egy korábbi lépésben már megadta a logic app hozzáférést az integrációs fiók, amely tartalmazza a térképet.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. A munka mentése  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Ezen a ponton befejezte a térkép beállítását. Egy valós alkalmazásokban előfordulhat, hogy az átalakított adatokat egy üzletági alkalmazásban, például a SalesForce-ban szeretné tárolni. Az átalakítás kimenetét egyszerűen elküldheti a Salesforce-nak. 

Most már tesztelheti az átalakítást a HTTP-végpontra vonatkozó kéréssel.  


## <a name="features-and-use-cases"></a>Funkciók és használati esetek
* A térképen létrehozott átalakítás egyszerű lehet, például egy név és cím másolása egyik dokumentumból a másikba. Összetettebb átalakításokat is létrehozhat a beépített térképműveletek használatával.  
* Több térképművelet vagy függvény is könnyen elérhető, beleértve a karakterláncokat, a dátumidő-függvényeket és így tovább.  
* Közvetlen adatmásolást tehet a sémák között. Az SDK-ban szereplő leképezőben ez olyan egyszerű, mint egy olyan vonal megrajzolása, amely összeköti a forrásséma elemeit a célsémában lévő megfelelőkkel.  
* Térkép létrehozásakor megtekintheti a térkép grafikus ábrázolását, amely az összes létrehozott kapcsolatot és hivatkozást megjeleníti.
* A Tesztleképezés szolgáltatással XML-mintaüzenetet adhat hozzá. Egy egyszerű kattintással tesztelheti a létrehozott térképet, és megtekintheti a generált kimenetet.  
* Meglévő térképek feltöltése  
* Támogatja az XML-formátumot.

## <a name="advanced-features"></a>Speciális funkciók

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referenciaszerelvény vagy egyéni kód térképekből 
Az átalakítási művelet külső összeállítással is támogatja a térképeket vagy az átalakításokat. Ez a funkció lehetővé teszi, hogy az egyéni .NET-kód hívásokat közvetlenül az XSLT-leképezésekről. Az alábbiakban a térképekszerelvény használatának előfeltételei tanaksznak.

* A térképről hivatkozott térképet és szerelvényt fel kell tölteni az [integrációs fiókba.](./logic-apps-enterprise-integration-maps.md) 

  > [!NOTE]
  > A térképet és a szerelvényt meghatározott sorrendben kell feltölteni. A szerelvényt fel kell töltenie, mielőtt feltöltené a szerelvényre hivatkozó térképet.

* A térképnek rendelkeznie kell ezekkel az attribútumokkal és egy CDATA szakaszsal, amely tartalmazza a szerelvénykód hívását:

    * **a név** az egyéni kódösszeállítás neve.
    * **A névtér** az a névtér a szerelvényben, amely tartalmazza az egyéni kódot.

  Ebben a példában egy "XslUtilitiesLib" nevű kódösszeállításra hivatkozó térkép látható, és a metódust a `circumreference` szerelvényből hívja meg.

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


### <a name="byte-order-mark"></a>Bájtrendelés-jel
Alapértelmezés szerint az átalakítás válasza a Bájtrendelés-jellel (AJ) kezdődik. Ezt a funkciót csak a Kódnézet szerkesztőben végzett munka közben érheti el. A funkció letiltásához `disableByteOrderMark` adja `transformOptions` meg a tulajdonsághoz:

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
* [További információ az Enterprise Integration Pack csomagról](../logic-apps/logic-apps-enterprise-integration-overview.md "További információ a vállalati integrációs csomagról")  
* [További információ a térképekről](../logic-apps/logic-apps-enterprise-integration-maps.md "További információ a vállalati integrációs térképekről")  

