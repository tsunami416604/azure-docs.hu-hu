---
title: Kezelheti a verziókat LUIS alkalmazásokban az Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan verzióinak nyelvi ismertetése (LUIS) alkalmazások kezelését.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: v-geberr
ms.openlocfilehash: 672f7991be0fc236e39daf7d1ce1d6080b31815b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347086"
---
# <a name="manage-versions"></a>Verziók kezelése

Minden alkalommal, amikor a modell munka hozzon létre egy másik [verzió](luis-concept-version.md) az alkalmazás. 

## <a name="set-active-version"></a>Aktív verzió beállítása
Verziók dolgozni, nyissa meg az alkalmazás választva módosíthatja a nevet **saját alkalmazások** lapon, majd válassza ki **beállítások** a felső sávon.

![Verziók lap](./media/luis-how-to-manage-versions/settings.png)

A **beállítások** lap lehetővé teszi, hogy a teljes alkalmazáshoz, beleértve a verziókkal, valamint a közreműködők beállításait is konfigurálhatja. 

## <a name="clone-a-version"></a>A verzió klónozása
1. Az a **beállítások** lapon után az alkalmazásbeállítások és közreműködők szakaszok megkeresése klónozni kívánt verziójával. Válassza ki a jobb szélső a három pontra (...). 

    ![Verzió sor tulajdonságai](./media/luis-how-to-manage-versions/version-section.png)

2. Válassza ki **Klónozás** a listából.

    ![Verzió sor tulajdonságok kiválasztása](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. Az a **Klónozás verzió** párbeszédpanelen írja be az új verzióra, például a "0,2" nevét.

   ![Klónozás verziójára párbeszédpanel](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > Verzió azonosítója csak karakterek, számjegyből állhat vagy ".", és nem lehet 10 karakternél hosszabb.
 
 A megadott nevű új verzió létrehozása és állítja be az aktív verzió.
 
  ![Verzió létrehozza és hozzáadja a listához](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > A fenti ábrán látható módon a közzétett verziót kapcsolódik a színes be van jelölve, ahol közzétette tárhely típusának: éles (zöld), átmeneti (piros), és mindkettő (fekete). A képzés és közzétételi dátumok jelennek meg minden egyes közzétett verziót.

## <a name="set-active-version"></a>Aktív verzió beállítása
1. Az a **beállítások** lap a **verziók** listára, válassza ki a három pont (…) jobb szélén.

2. A legördülő listából válassza ki a **aktív**.

    ![Aktív verzió beállítása](./media/luis-how-to-manage-versions/set-active-version.png)

    Az aktív verzió egy világos kék szín szerinti be van jelölve, az alábbi képernyőfelvételen látható módon:

    ![Az aktív verzió](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Importálás verziója
A verzió importálhat egy JSON-fájlt. Importálja a verziója, az új verzió aktív verziója lesz.

**A verzió importálása:**

1. Az a **beállítások** lapon jelölje be **importálási új verzió** gombra.

    ![Importálás gomb](./media/luis-how-to-manage-versions/import-version.png)

2. Válassza ki **Tallózás** , és válassza a JSON-fájlt.

    ![Importálás verzió párbeszédpanel](./media/luis-how-to-manage-versions/import-version-dialog.png)

Csak kell beállítani egy Verzióazonosítót, ha az alkalmazás már szerepel a JSON-fájl verzióját.

## <a name="export-version"></a>Export verzió
JSON-fájlba exportálhatja egy verzió.

**A verzió exportálása:**

1. Az a **beállítások** lap a **verziók** listára, válassza ki a három pont (…) jobb szélén.

2. Válassza ki **exportálása** műveleteket, és válassza ki, ha a fájlt menteni szeretné a előugró listájában.

## <a name="delete-a-version"></a>A verzió törlése
Törölheti a verziók, de tartsa meg az alkalmazás legalább egy verziója van. Az aktív verzió kivételével az összes verzió törlése. 

1. Az a **beállítások** lap a **verziók** listára, válassza ki a három pont (…) jobb szélén.

2. Válassza ki **törlése** műveleteket, és válassza ki, ha a fájlt menteni szeretné a előugró listájában.

    ![Törlés verzió megerősítése](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Nevezze át a verzió
Átnevezheti verziók mindaddig, amíg a verzió neve már nem használja.  

1. Az a **beállítások** lap a **verziók** listára, válassza ki a három pont (…) jobb szélén.

2. Válassza ki **átnevezése** műveletek előugró listájában.

3. Adja meg az új verzió nevét, és válassza ki **végzett**.

    ![Nevezze át a verzió megerősítése](./media/luis-how-to-manage-versions/rename-popup.png) 
