<properties
    pageTitle="Logic Apps alkalmazások áttelepítése a 2015. 08. 01. dátumú előzetes sémaverzióra | Microsoft Azure App Service"
    description="Logic Apps alkalmazásait egyszerűen áttelepítheti a legújabb sémaverzióra. Csak kövesse az alábbi lépéseket."
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>

# Logic Apps alkalmazások áttelepítése a 2015. 08. 01. dátumú előzetes sémaverzióra

A meglévő Logic Apps alkalmazásoknak az új sémára való áttelepítéséhez tegye az alábbiakat:  
1. Nyissa meg a Logic Apps alkalmazást az Azure Portalon  
2. Kattintson az Update Schema (Séma frissítése) elemre:

 ![API-ikon][step1]   
Megjelenik az Update Schema (Séma frissítése) lap az új sémában található fejlesztések részleteit tartalmazó dokumentumra mutató hivatkozással: ![API-ikon][step2]

>[AZURE.NOTE] Az **Update Schema** (Séma frissítése) választásakor automatikusan futtatjuk az áttelepítési lépéseket, és átadjuk Önnek a kódkimenetet. Ennek használatával frissítheti a definíciót, azonban mindenképpen kövesse a kódolás ajánlott eljárásit, például az alábbi **Ajánlott eljárások** című szakaszban foglaltakat.

## Ajánlott eljárások a Logic Apps alkalmazások áttelepítéséhez a legújabb sémaverzióra:  

- Az áttelepített parancsfájlt új Logic Apps alkalmazásba másolja – ne írja felül a régit, amíg el nem végezte a tesztelést, és meg nem győződött róla, hogy az áttelepített alkalmazás a vártnak megfelelően működik.
- Tesztelje a Logic Apps alkalmazást, **mielőtt** éles környezetben használná.
- Az áttelepítés befejezését követően kezdje el frissíteni a Logic Apps alkalmazásokat a [felügyelt API-k](./apis-list.md) használatára, ha lehetséges. Megkezdheti például a Dropbox 2-es verziójának használatát azon alkalmazások esetén, amelyek a DropBox 1-es verzióját használják.


## A következő lépések
-  [Információk a Logic Apps alkalmazások manuális áttelepítésével kapcsolatban](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[1. lépés]: ./media/connectors-schema-migration/migrateschema1.png
[2. lépés]: ./media/connectors-schema-migration/migrateschema2.png









<!--HONumber=sep16_HO1-->


