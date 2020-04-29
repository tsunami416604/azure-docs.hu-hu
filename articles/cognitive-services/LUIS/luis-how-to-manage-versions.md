---
title: Verziók kezelése – LUIS
titleSuffix: Azure Cognitive Services
description: A verziók segítségével különböző modelleket hozhat létre és tehet közzé. A modell módosítása előtt célszerű a jelenlegi aktív modell klónozását az alkalmazás egy másik verziójára.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220896"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Verziók használata az átmeneti vagy éles alkalmazások befolyásolása nélkül

A verziók segítségével különböző modelleket hozhat létre és tehet közzé. A modell módosítása előtt célszerű a jelenlegi aktív modell klónozását az alkalmazás egy másik [verziójára](luis-concept-version.md) . 

A verziókhoz való munkavégzéshez nyissa meg az alkalmazást a saját **alkalmazások** lapján, majd válassza a **kezelés** lehetőséget a felső sávon, majd válassza a **verziók** elemet a bal oldali navigációs felületen. 

A verziók listája megjeleníti a közzétett verziókat, a közzétételük helyét, valamint a jelenleg aktív verziót. 

> [!div class="mx-imgBorder"]
> [![Kezelés szakasz, verziók lap](./media/luis-how-to-manage-versions/versions-import.png "Kezelés szakasz, verziók lap")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Verzió klónozása

1. Válassza ki a klónozott verziót, majd válassza a **klónozás** lehetőséget az eszköztáron. 

2. A **klón verziója** párbeszédpanelen írja be az új verzió nevét, például: "0,2".

   ![Verzió klónozása párbeszédpanel](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > A verzióazonosító csak karakterből, számjegyből vagy "." állhat, és nem lehet hosszabb 10 karakternél.
 
   A rendszer létrehoz egy új, a megadott névvel ellátott verziót, és beállítja az aktív verziót.

## <a name="set-active-version"></a>Aktív verzió beállítása

Válasszon egy verziót a listából, majd kattintson az **aktiválás** elemre az eszköztáron. 

> [!div class="mx-imgBorder"]
> [![A szakasz, a verziók lap, a verzióra vonatkozó művelet kezelése](./media/luis-how-to-manage-versions/versions-other.png "A szakasz, a verziók lap, a verzióra vonatkozó művelet kezelése")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importálási verzió

Importálhatja az alkalmazás `.json` egy vagy `.lu` több verzióját is.

1. Válassza az **Importálás** lehetőséget az eszköztárból, majd válassza ki a formátumot. 

2. Az **új verzió importálása** előugró ablakban adja meg az új tíz karakteres verzió nevét. Csak akkor kell megadnia a verziószámot, ha a fájl verziója már létezik az alkalmazásban.

    ![Szakasz, verziók lap, új verzió importálása](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    A verzió importálása után az új verzió lesz az aktív verzió.

### <a name="import-errors"></a>Importálási hibák

* Tokenizer hibák: Ha az importálás során **Tokenizer hibaüzenetet** kap, egy olyan verziót próbál importálni, amely a jelenleg használt alkalmazástól eltérő [Tokenizer](luis-language-support.md#custom-tokenizer-versions) használ. A probléma megoldásához tekintse meg az [áttelepítés a tokenizer-verziók között](luis-language-support.md#migrating-between-tokenizer-versions)című témakört.

<a name = "export-version"></a>

## <a name="other-actions"></a>Egyéb műveletek

* Ha **törölni** szeretne egy verziót, válasszon ki egy verziót a listából, majd válassza a **Törlés** lehetőséget az eszköztárból. Kattintson **az OK gombra**. 
* Egy verzió **átnevezéséhez** válasszon egy verziót a listából, majd válassza az **Átnevezés** lehetőséget az eszköztárból. Adja meg az új nevet, és válassza a **kész**lehetőséget. 
* Egy verzió **exportálásához** válasszon ki egy verziót a listából, majd válassza az **alkalmazás exportálása** lehetőséget az eszköztárból. Válassza ki a JSON-t a biztonsági mentéshez való exportáláshoz, majd válassza az **Exportálás tárolóként** lehetőséget az [alkalmazás egy Luis-tárolóban való használatához](luis-container-howto.md).  

