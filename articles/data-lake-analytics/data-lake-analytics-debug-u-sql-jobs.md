---
title: "U-SQL feladatok hibakeresése |} Microsoft Docs"
description: "Útmutató egy U-SQL Visual Studio használatával sikertelen csúcspont hibakereséséhez."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Felhasználó által definiált C# programkódja sikertelen U-SQL feladatok hibakeresése

U-SQL egy bővíthetőségi modell használatával C#, így a kód hozzáadása a Funkciók, például egy egyéni készülék vagy nyomáscsökkentő írhat biztosít. További tudnivalókért lásd: [U-SQL programozástámogatási útmutató](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). A gyakorlatban kódok hibakeresés esetleg, és a big data-rendszereket csupán korlátozott futási hibakeresési információk, például a naplófájlok.

Az Azure Data Lake Tools for Visual Studio nevű funkcióval rendelkezik **csúcspont Debug nem sikerült**, amellyel klónozza a felhőből sikertelen feladat a helyi számítógépen a hibakereséshez. A helyi másolat a teljes felhőkörnyezetben, beleértve a bemeneti adatok és a felhasználói kód rögzíti.

A következő videó bemutatja a csúcspont Debug nem sikerült Azure Data Lake Tools for Visual Studio.

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> A Visual Studio a következő két frissítésekre van szüksége, ha még nincs telepítve: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) és a [Universal C futásidejű for Windows](https://www.microsoft.com/download/details.aspx?id=50410).

## <a name="download-failed-vertex-to-local-machine"></a>Nem sikerült letölteni a csúcspont helyi géphez

Amikor megnyit sikertelen feladat a Azure Data Lake Tools for Visual Studio, egy sárga értesítési sáv részletes hibaüzeneteket a hiba lapon látható.

1. Kattintson a **letöltése** letölteni a szükséges erőforrások és a bemeneti adatfolyam. Kattintson a letöltés indul el, ha **újra**.

2. Kattintson a **nyitott** helyi hibakeresési környezetben létrehozásához a letöltés befejezése után. Az automatikusan létrehozott és megnyitott egy új Visual Studio-példány és a hibakeresési megoldás.

![Az Azure Data Lake Analytics U-SQL hibakeresési visual studio letöltése csúcspont](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

Feladatok közé tartozhat a háttérkód forrásfájlok vagy a regisztrált szerelvényeket, és két különböző hibakeresési forgatókönyvek rendelkezik.

- [Sikertelen feladat a háttérkód hibakeresése](#debug-job-failed-with-code-behind)
- [Sikertelen feladat a hibakereséshez szerelvények](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>Feladat sikertelen volt a háttérkód hibakeresése

Ha egy U-SQL-feladat sikertelen lesz, és a feladat tartalmazza a felhasználói kód (általában nevű `Script.usql.cs` U-SQL projekt), hogy forráskód importálta-e a hibakeresési megoldás.  Innen a Visual Studio hibakereső eszközöket (figyelési, változókat, stb.) a probléma elhárításához is használhatja.

> [!NOTE]
> Mielőtt hibakeresés, ügyeljen arra, hogy ellenőrizze **közös nyelvi futtatókörnyezet kivételek** a kivétel beállítások ablakban (**Ctrl + Alt + E**).

![Az Azure Data Lake Analytics U-SQL hibakeresési visual studio-beállítás](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. Nyomja le az **F5** a háttérkód kód futtatására. Akkor fog futni, amíg egy kivétel miatt leállt.

2. Nyissa meg a `ADLTool_Codebehind.usql.cs` fájlt, és állítson be töréspontokat, majd nyomja le az **F5** a kód lépésenkénti hibakereséséhez.

    ![Az Azure Data Lake Analytics U-SQL hibakeresési kivétel](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>Feladat sikertelen volt a szerelvények hibakeresését

Ha regisztrált szerelvényeket a U-SQL parancsfájlt használja, a rendszer automatikusan a forráskód nem lehet lekérdezni. Ebben az esetben adja hozzá manuálisan a szerelvények forráskódfájl a megoldáshoz.

### <a name="configure-the-solution"></a>A megoldás konfigurálása

1. Kattintson a jobb gombbal **megoldás "VertexDebug" > vegye fel > létező projekt...**  keresése a szerelvények forráskódot, és adja hozzá a projekt hibakeresési megoldás.

    ![Az Azure Data Lake Analytics U-SQL hibakeresési projekt hozzáadása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Kattintson a jobb gombbal **LocalVertexHost > Tulajdonságok** a megoldás, és másolja a **munkakönyvtár** elérési útja.

3. Kattintson a jobb gombbal **forrás kód szerelvényprojektet > Tulajdonságok**, jelölje be a **Build** bal oldali lapon, és illessze be a másolt elérési útjára **kimeneti > elérési utat a kimeneti**.

    ![Az Azure Data Lake Analytics U-SQL hibakeresési pdb elérési útjának beállítása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. Nyomja le az **Ctrl + Alt + E**, ellenőrizze **közös nyelvi futtatókörnyezet kivételek** kivétel beállításai ablakban.

### <a name="start-debug"></a>Indítsa el a hibakeresési

1. Kattintson a jobb gombbal **szerelvény forráskód Projekt > újraépítése** kimeneti .pdb fájlt a `LocalVertexHost` munkakönyvtárát.

2. Nyomja le az **F5** és a projekt fog futni, amíg egy kivétel miatt leállt. A következő figyelmeztető üzenet, amely biztonságosan figyelmen kívül hagyhatja jelenhet meg. Azt is eltarthat egy percet a hibakeresési képernyő eléréséhez.

    ![Az Azure Data Lake Analytics U-SQL hibakeresési visual studio figyelmeztetés](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. Nyissa meg a forráskódot, és állítson be töréspontokat, majd nyomja le az **F5** a kód lépésenkénti hibakereséséhez.

A Visual Studio hibakereső eszközöket (figyelési, változókat, stb.) a probléma elhárításához is használhatja.

> [!NOTE]
> A szerelvény forráskód projekt újraépítése frissített .pdb fájlok létrehozásához a kód módosítása után minden alkalommal.

Hibakeresési információ után a projekt sikeresen befejeződik a kimeneti ablakban látható a következő üzenetet:

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Az Azure Data Lake Analytics U-SQL hibakeresési sikeres](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>Küldje el újra a feladatot

Ha befejezte a hibakeresést, küldje el újra a sikertelen feladatot.

1. A feladatok a háttérkód megoldásokkal, másolja a C#-kódban a háttérkód forrásfájl (általában `Script.usql.cs`).
2. A szerelvények feladatok a frissített .dll szerelvényeket a ADLA-adatbázisba regisztrálása:
    1. A Server Explorer vagy a Cloud Explorerben bontsa ki a **ADLA fiók > adatbázisok** csomópont.
    2. Kattintson a jobb gombbal **szerelvények** , és regisztrálja az új .dll szerelvényeket a ADLA adatbázissal: ![Azure Data Lake Analytics U-SQL hibakeresési szerelvény regisztrálása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. Küldje el újra a feladatot.

## <a name="next-steps"></a>Következő lépések

- [U-SQL programozástámogatási útmutató](data-lake-analytics-u-sql-programmability-guide.md)
- [Azure Data Lake Analytics-feladatok U-SQL-felhasználó által definiált operátorok fejlesztése](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Oktatóanyag: Data Lake Tools for Visual Studio használatával U-SQL-parancsfájlok fejlesztése](data-lake-analytics-data-lake-tools-get-started.md)
