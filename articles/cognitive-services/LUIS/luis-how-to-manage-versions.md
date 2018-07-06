---
title: A LUIS-alkalmazások az Azure-ban verziók kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a verziók a Language Understanding (LUIS) alkalmazások.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: v-geberr
ms.openlocfilehash: ef5dadd94d3612500d3092bdbd601fdaa12d1701
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868020"
---
# <a name="manage-versions"></a>Verziók kezelése

Minden alkalommal, amikor a modell végzett munka hozzon létre egy másik [verzió](luis-concept-version.md) az alkalmazás. 

## <a name="set-active-version"></a>Aktív verzió beállítása
Verzióival működnek, nyissa meg az alkalmazás nevére a kiválasztásával **saját alkalmazások** oldalra, és kattintson **beállítások** a lap tetején található.

![Verziók lap](./media/luis-how-to-manage-versions/settings.png)

A **beállítások** lap lehetővé teszi a teljes alkalmazás, beleértve a verziója, és a közreműködők beállításainak konfigurálása. 

## <a name="clone-a-version"></a>Klónozza a verzió
1. Az a **beállítások** lapon az alkalmazásbeállítások és a közreműködők szakaszok után megkeresi a klónozni kívánt verziójával. Kattintson a három pontra (***...*** ) gombra a jobb sarkában. 

    ![Verzió sor tulajdonságai](./media/luis-how-to-manage-versions/version-section.png)

2. Válassza ki **Klónozás** a listából.

    ![Verzió sor tulajdonságok kiválasztása](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. Az a **Klónozás verzió** párbeszédpanelen írjon be egy nevet az új verziót, például a "0.2-es".

   ![Klónozás verziójára párbeszédpanel](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > Verzió azonosítója csak karakterek, számjegyek állhat vagy '.', és nem lehet hosszabb 10 karakter hosszúságú.
 
 A megadott nevű új verzióját, és a állítja be aktív verziója.
 
  ![Verzió létrehozása és hozzáadása a listához](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > Amint az előző képen látható, közzétett verziót társítva a színes be van jelölve, ahol közzétette tárhely típusának: éles (zöld), átmeneti (vörös), és mindkettő (fekete). A képzés és a dátumok megjelenik az egyes közzétett verziót.

## <a name="set-active-version"></a>Aktív verzió beállítása
1. Az a **beállítások** lap a **verziók** listájához, válassza ki a három pontra (***...*** ) gomb jobb szélén.

2. A legördülő listából válassza ki a **aktív**.

    ![Aktív verzió beállítása](./media/luis-how-to-manage-versions/set-active-version.png)

    Az aktív verzió kiemelten egy világos kék, az alábbi képernyőképen látható módon:

    ![Az aktív-verzió](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Importálás verzió
A verzió importálhat egy JSON-fájlt. -Verziót importált, miután az új verzió válik az aktív verziót.

**A verzió importálása:**

1. A a **beállítások** lapon jelölje be **importálása új verzió** gombra.

    ![Importálás gomb](./media/luis-how-to-manage-versions/import-version.png)

2. Válassza ki **Tallózás** , és válassza ki a JSON-fájlt.

    ![Importálás verzió párbeszédpanel](./media/luis-how-to-manage-versions/import-version-dialog.png)

Csak egy beállítani, ha a verzió a JSON-fájl már létezik az alkalmazásban kell.

## <a name="export-version"></a>Verzió exportálása
A verzió egy JSON-fájlba exportálhatja.

**A verzió exportálása:**

1. Az a **beállítások** lap a **verziók** listájához, válassza ki a három pontra (***...*** ) gomb jobb szélén.

2. Válassza ki **exportálása** műveleteket, és válassza ki, ahol a fájlt menteni szeretné az előugró listájában.

## <a name="delete-a-version"></a>A verzió törlése
Verziók törölheti, de rendelkezik, így az alkalmazás legalább egy verziója. Az aktív verzió kivételével minden verzió törlése 

1. Az a **beállítások** lap a **verziók** listájához, válassza ki a három pontra (***...*** ) gomb jobb szélén.

2. Válassza ki **törlése** műveleteket, és válassza ki, ahol a fájlt menteni szeretné az előugró listájában.

    ![Törlés megerősítése verzió](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Nevezze át a verzió
Verziók átnevezheti mindaddig, amíg a verzió neve nem már használatban van.  

1. Az a **beállítások** lap a **verziók** listájához, válassza ki a három pontra (***...*** ) gomb jobb szélén.

2. Válassza ki **átnevezése** műveletek legördülő listájában.

3. Adja meg az új verzió nevét, és válassza ki **kész**.

    ![Nevezze át a verzió megerősítése](./media/luis-how-to-manage-versions/rename-popup.png) 
