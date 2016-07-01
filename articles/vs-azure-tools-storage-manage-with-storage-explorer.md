<properties
    pageTitle="Ismerkedés a Tártallózó alkalmazással (előzetes verzió) | Microsoft Azure"
    description="Azure tárerőforrások kezelése a Tártallózó alkalmazással (előzetes verzió)"
    services="visual-studio-online"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="visual-studio-online"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/05/2016"
    ms.author="tarcher" />

# Ismerkedés a Tártallózó alkalmazással (előzetes verzió)

## Áttekintés 

A Microsoft Azure Tártallózó (előzetes verzió) egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, OSX és Linux rendszereken. Ebben a cikkben megismerheti az Azure-tárfiókok csatlakoztatásának és kezelésének különféle módjait.

## Előfeltételek

- [A Tártallózó (előzetes verzió) letöltése és telepítése](http://go.microsoft.com/fwlink/?LinkId=708343)

## Csatlakozás egy tárfiókhoz vagy -szolgáltatáshoz

A Tártallózó (előzetes verzió) számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz. Ezek a következők lehetnek: csatlakozás az Azure-előfizetésekkel társított tárfiókokhoz, csatlakozás a más Azure-előfizetésekből megosztott tárfiókokhoz és -szolgáltatásokhoz, vagy akár a helyi tárterület csatlakoztatása és kezelése az Azure Storage Emulator használatával:

- [Csatlakozás Azure-előfizetéshez](#connect-to-an-azure-subscription) – Az Azure-előfizetéséhez tartozó tárolási erőforrások kezelése.
- [Csatlakozás helyi tárterülethez](#connect-to-local-storage) – Helyi tárterület kezelése az Azure Storage Emulator használatával. 
- [Külső tárterület csatolása](#attach-or-detach-an-external-storage-account) – Más Azure-előfizetések alá tartozó tárolási erőforrások kezelése a tárfiók fióknevének és kulcsának használatával.
- [Fiók csatolása SAS használatával](#attach-account-using-sas) – Más Azure-előfizetések alá tartozó tárolási erőforrások kezelése SAS használatával.
- [Szolgáltatás csatolása SAS használatával](#attach-service-using-sas) – Más Azure-előfizetések alá tartozó adott tárolási szolgáltatás (blob tároló, üzenetsor vagy tábla) kezelése SAS használatával.

## Csatlakozás Azure-előfizetéshez

> [AZURE.NOTE] Ha nincs Azure-fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja Visual Studio-előfizetése kiemelt előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Indítsa el a Tártallózót (előzetes verzió). 

1. Amennyiben első alkalommal futtatja a Tártallózót (előzetes verzió), vagy ha már futtatta korábban, de még nem kapcsolódott egy Azure-fiókhoz, egy információs sáv jelenik meg, amelynek segítségével csatlakoztathatja egy Azure-fiókhoz.

    ![][0]
    
1. Válassza a **Csatlakozás a Microsoft Azure szolgáltatáshoz** lehetőséget, és a párbeszédpanelek útmutatásait követve jelentkezzen be egy olyan Microsoft-fiókkal, amely legalább egy aktív Azure-előfizetéssel társítva van.

Amint sikeresen bejelentkezett egy Microsoft-fiókkal, a Tártallózó (előzetes verzió) bal oldali ablaktábláján megjelenik a Microsoft-fiókkal társított összes Azure-előfizetéshez rendelt minden tárfiók.

### Azure-előfizetések szűrése

A Tártallózó (előzetes verzió) használatával megszűrheti a listát, és kiválaszthatja, hogy melyik bejelentkezett Microsoft-fiók(ok)kal társított Azure-előfizetések tárfiókjai jelenjenek meg a bal oldali ablaktáblán.

1. Válassza a **Beállítások** (fogaskerék) ikont.

    ![][1]   

1.  A bal oldali ablaktábla tetején megjelenik egy legördülő lista az összes Microsoft-fiókkal, amelybe be van jelentkezve. 

    ![][3]
     
1.  A legördülő lista melletti lefelé mutató nyilat választva megtekintheti összes Microsoft-fiókját, ahova be van jelentkezve, valamint egy hivatkozást, amellyel további Microsoft-fiókokba jelentkezhet be.

    ![][4]

1.  Válassza ki a kívánt Microsoft-fiókot a legördülő listából.

1.  A bal oldali ablaktábla megjeleníti a kiválasztott Microsoft fiókkal társított összes Azure-előfizetést.
Az egyes Azure-előfizetések mellett lévő jelölőnégyzetek segítségével adhatja meg, hogy a Tártallózó (előzetes verzió) listázza-e az adott Azure-előfizetéssel társított tárfiókokat. Az **Összes előfizetés** lehetőség bejelölésével kijelölheti az összes felsorolt Azure-előfizetést, vagy törölheti mindegyik kijelölését.

    ![][2]  

1.  Miután végzett a kezelni kívánt Azure-előfizetések kiválasztásával, kattintson az **Alkalmaz** gombra. A bal oldali ablaktábla frissítve lesz, és az aktuális Microsoft-fiók választott Azure-előfizetéseinek összes tárfiókját listázza majd. 

### További Microsoft-fiókok hozzáadása

A következő lépések elvégzésével további Microsoft-fiókokat csatlakoztathat, és megtekintheti mindegyik fiók társított Azure-előfizetéseit és tárfiókjait.

1.  Válassza a **Beállítások** (fogaskerék) ikont.

    ![][1]   

1.  A bal oldali ablaktábla tetején megjelenik egy legördülő lista az összes Microsoft-fiókkal, amelyhez aktuálisan csatlakozik.

    ![][3]
     
1.  A legördülő lista melletti lefelé mutató nyilat választva megtekintheti összes Microsoft-fiókját, ahova be van jelentkezve, valamint egy hivatkozást, amellyel további Microsoft-fiókokba jelentkezhet be.

    ![][4]

1.  Válassza a **Fiók hozzáadása** lehetőséget, és a párbeszédpanelek útmutatásait követve jelentkezzen be egy olyan fiókkal, amely legalább egy aktív Azure-előfizetéssel társítva van.

1.  Jelölje be az átnézni kívánt Azure-előfizetések jelölőnégyzeteit. 

    ![][2]  

1.  Kattintson az **Alkalmaz** gombra.

### Váltás a Microsoft-fiókok között

Bár több Microsoft-fiókhoz is csatlakozhat, a bal oldali ablaktábla csupán egyetlen, a jelenlegi Microsoft-fiók előfizetéseivel társított tárfiókokat mutatja. Ha több Microsoft-fiókhoz csatlakozik, az alábbi lépések végrehajtásával válthat a fiókok közt:

1.  Válassza a **Beállítások** (fogaskerék) ikont.

    ![][1]   

1.  A bal oldali ablaktábla tetején megjelenik egy legördülő lista az összes Microsoft-fiókkal, amelyhez aktuálisan csatlakozik.

    ![][3]
     
1.  A legördülő lista melletti lefelé mutató nyilat választva megtekintheti összes Microsoft-fiókját, ahova be van jelentkezve, valamint egy hivatkozást, amellyel további Microsoft-fiókokba jelentkezhet be.

    ![][4]

1.  Válassza ki a kívánt Microsoft-fiókot.

1.  Jelölje be az átnézni kívánt Azure-előfizetések jelölőnégyzeteit. 

    ![][2]  

1.  Kattintson az **Alkalmaz** gombra.
  
## Csatlakozás helyi tárterülethez

A Tártallózó (előzetes verzió) segítségével a helyi tárterületen is dolgozhat az Azure Storage Emulator használatával. Így anélkül is írhat kódot a tárterületre és tesztelheti azt, hogy szüksége lenne egy telepített tárfiókra az Azure szolgáltatásban (mivel a tárfiókot az Azure Storage Emulator emulálja).

>[AZURE.NOTE] Az Azure Storage Emulator jelenleg kizárólag Windows rendszeren támogatott. 

1. Indítsa el a Tártallózót (előzetes verzió). 

1. A bal oldali ablaktáblán bontsa ki a **(Fejlesztés)** csomópontot.

    ![][21]

1. Ha az Azure Storage Emulator még nincs telepítve, a rendszer az információs sávon kéri erre. Ha az információs sáv megjelenik, válassza a **Legújabb verzió letöltése** lehetőséget, és telepítse az emulátort. 

    ![][22]

1. Miután telepítette az emulátort, létrehozhat majd helyi blobokat, üzenetsorokat és táblákat, és dolgozhat ezekkel. Az egyes tárfióktípusok kezelésével kapcsolatos információkért kattintson alább a megfelelő hivatkozásra:

    - [Azure Blob Storage-erőforrások kezelése](./vs-azure-tools-storage-explorer-blobs.md)
    - Azure Queue Storage-erőforrások kezelése – *Hamarosan elérhető*
    - Azure Table Storage-erőforrások kezelése – *Hamarosan elérhető*

## Külső tárfiók csatolása vagy leválasztása

A Tártallózó (előzetes verzió) segítségével külső tárfiókokat csatolhat, így azok könnyen megoszthatóak. Ez a szakasz külső tárfiókok csatolását (és leválasztását) írja le.

### Tárfiók hitelesítő adatainak lekérése

A külső tárfiókok megosztásához először az adott fiók tulajdonosának le kell kérnie a fiók hitelesítő adatait (a fióknevet és a kulcsot), majd meg kell osztania azokat a – külső – fiókot csatlakoztatni kívánó személlyel. A tárfiók hitelesítő adatainak lekérése az Azure portálon keresztül lehetséges az alábbi lépések végrehajtásával: 

1.  Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1.  Válassza a **Tallózás** lehetőséget.
1.  Válassza a **Tárfiókok** lehetőséget.
1.  A **Tárfiókok** panelen válassza ki a kívánt tárfiókot.
1.  A kiválasztott tárfiók **Beállítások** panelén válassza a **Hívóbetűk** lehetőséget.

    ![][5]
    
1.  A **Hívóbetűk** panelen másolja ki a **TÁRFIÓK NEVE** és **KULCS 1** értékeket, a tárfiók csatolásához. 

    ![][6]

### Külső tárfiók csatolása

1.  A Tártallózó (előzetes verzió) alkalmazásban kattintson a jobb gombbal a **Tárfiókok** lehetőségre, és válassza – a helyi menüből – a **Külső tárterület csatolása** lehetőséget.

    ![][7]
    
1.  A *Tárfiók hitelesítő adatainak lekérése* szakasz ismerteti a tárfiók nevének és a kulcs 1 értékének lekérését. Ebben a lépésben ezeket az értékeket használjuk majd. A **Külső tárterület csatolása** párbeszédpanelen adja meg a tárfiók nevét a **Fióknév**, a Kulcs 1 értékét a **Fiókkulcs** mezőben. Kattintson az **OK** gombra, amikor végzett. 

    ![][8]

    A csatolást követően a külső tárfiók a nevét követő **(Külső)** megjelöléssel jelenik meg a tárfiókok közt. 

    ![][9]

### Külső tárfiók leválasztása

1.  Kattintson a jobb gombbal a leválasztani kívánt külső tárfiókra, és válassza – a helyi menüből – a **Leválasztás** lehetőséget.

    ![][10]

1.  Amikor a megerősítő üzenet megjelenik, kattintson az **Igen** gombra a külső tárfiók leválasztásának jóváhagyásához.

    ![][12]

## Fiók csatolása SAS használatával

A SAS (Shared Access Signature, vagyis közös hozzáférésű jogosultságkód) lehetővé teszi, hogy az Azure-előfizetés rendszergazdája ideiglenes hozzáférést engedélyezzen a tárfiókhoz anélkül, hogy kiadná az Azure-előfizetés hitelesítő adatait. 

Ennek szemléltetésére tegyük fel, hogy az „A” felhasználó valamely Azure-előfizetés rendszergazdája, és hozzáférést szeretne engedélyezni „B” felhasználó számára a tárfiókhoz adott időtartamra és meghatározott engedélyekkel:

1. Az „A” felhasználó létrehoz egy SAS-kódot (amely a tárfiók kapcsolati karakterlánca) egy adott időtartamra és a kívánt engedélyekkel.
1. Az „A” felhasználó megosztja a SAS-kódot a tárfiókhoz hozzáférést igénylő személlyel – esetünkben a „B” felhasználóval.  
1. A „B” felhasználó a Tártallózó (előzetes verzió) segítségével csatolja az „A” felhasználóhoz tartozó fiókot a megadott SAS-kód használatával. 

### SAS-kód lekérése a megosztani kívánt fiókhoz

1.  Nyissa meg a Tártallózót (előzetes verzió).
1.  A bal oldali panelen kattintson a jobb gombbal a megosztani kívánt külső tárfiókra, és válassza – a helyi menüből – a **Közös hozzáférésű jogosultságkód igénylése** lehetőséget.

    ![][13]

1. A **Közös hozzáférésű jogosultságkód** párbeszédpanelen adja meg a kívánt időtartamot és engedélyeket a fiókhoz, és kattintson a **Létrehozás** gombra.

    ![][14]
 
1. Megjelenik egy második **Közös hozzáférésű jogosultságkód** párbeszédpanel a SAS-kóddal. Kattintson a **Másolás** parancsra a **Kapcsolati karakterlánc** mellett annak a vágólapra másolásához. Válassza a **Bezárás** lehetőséget a párbeszédpanel bezárásához.

### Közös fiók csatolása a SAS használatával

1.  Nyissa meg a Tártallózót (előzetes verzió).
1.  A bal oldali ablaktáblán kattintson jobb gombbal a **Tárfiókok** lehetőségre, és válassza – a helyi menüből – a **Fiók csatolása SAS használatával** lehetőséget.
    ![][15]

1. A **Fiók csatolása SAS használatával** párbeszédpanelen:

    - **Fióknév** – Adja meg a fiókhoz rendelni kívánt nevet. **MEGJEGYZÉS:** A fióknévnek nem szükséges egyeznie a tárfiók eredeti nevével, amelyhez a SAS létre lett hozva. 
    - **Kapcsolati karakterlánc** – Illessze be a korábban kimásolt kapcsolati karakterláncot.
    - Kattintson az **OK** gombra, amikor végzett.
    
    ![][16]

A csatolást követően a tárfiók a megadott fióknevet követő (SAS) megjelöléssel jelenik meg a tárfiókok közt.

![][17]

## Szolgáltatás csatolása SAS használatával

A [Fiók csatolása SAS használatával](#attach-account-using-sas) szakasz mutatja be, hogyan adhat az Azure-előfizetés rendszergazdája ideiglenes hozzáférést a tárfiókhoz a tárfiók SAS-kódjának létrehozásával (és megosztásával). Hasonlóképpen, SAS-kód létrehozható adott szolgáltatásokhoz (blob tárolókhoz, üzenetsorokhoz és táblákhoz) is a tárfiókon belül.  

### SAS-kód létrehozása a megosztani kívánt szolgáltatáshoz

Ebben a kontextusban a szolgáltatások blob tárolók, üzenetsorok vagy táblák lehetnek. Az alábbi szakaszok ismertetik a SAS-kód létrehozását az egyes felsorolt szolgáltatásokhoz:

- [SAS lekérése blob tárolóhoz](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- SAS lekérése üzenetsorhoz – *Hamarosan elérhető*
- SAS lekérése táblához – *Hamarosan elérhető*

### Közös fiókszolgáltatás csatolása a SAS használatával

1.  Nyissa meg a Tártallózót (előzetes verzió).
1.  A bal oldali ablaktáblán kattintson a jobb gombbal a **Tárfiókok** lehetőségre, és válassza – a helyi menüből – a **Szolgáltatás csatolása SAS használatával** lehetőséget.
    ![][18]

1. A **Fiók csatolása SAS használatával** párbeszédpanelen illessze be a korábban kimásolt SAS URI azonosítót, és kattintson az **OK** gombra.

    ![][19]

A csatolást követően az újonnan csatolt szolgáltatás a **(Szolgáltatás SAS)** csomópont alatt jelenik meg. 

![][20]

## Tárfiókok keresése

Amennyiben tárfiókjai listája túl hosszú, az adott tárfiókok megtalálásának egyszerű módja lehet a keresőmező használata a bal oldali ablaktábla tetején. 

Ahogy elkezdi beírni a szöveget a keresőmezőbe, a bal oldali ablaktábla csak azokat a tárfiókokat jeleníti meg, amelyek tartalmazzák az addig beírt szövegre adott találatokat. Az alábbi képernyőfelvételen egy olyan eset látható, ahol az összes tárfiók közt azokat a tárfiókokat kerestem, amelyeknek a neve tartalmazza a „tarcher” karakterláncot.

![][11]
    
A keresés törléséhez kattintson az **x** gombra a keresőmezőben.

## További lépések
- [Azure Blob Storage-erőforrások kezelése a Tártallózó (előzetes verzió) használatával](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png



<!--HONumber=Jun16_HO2--->


