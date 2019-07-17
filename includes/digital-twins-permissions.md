---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 06/28/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 324f41055cf333081f308a3ff533ff7df6b33038
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "67479199"
---
>[!NOTE]
>Ez a szakasz útmutatást nyújt a [új Azure AD-alkalmazás regisztrációjának](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Ha továbbra is az örökölt natív alkalmazás regisztrálása, használhatja azt is támogatott. Emellett ha valamilyen okból az új alkalmazás regisztrációs lehetőség nem működik a beállításokban, előfordulhat, hogy megpróbál létrehozni egy örökölt natív AAD-alkalmazás. Olvasási [az Azure digitális Twins alkalmazás regisztrálása az Azure Active Directory örökölt](../articles/digital-twins/how-to-use-legacy-aad.md) kapcsolatos további információkat. 

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg **Azure Active Directory** a bal oldali ablaktáblán, és ezután nyissa meg a **alkalmazásregisztrációk** ablaktáblán. Válassza ki a **új regisztrációs** gombra.

    ![Regisztrált alkalmazás](./media/digital-twins-permissions/aad-app-register.png)

1. Adjon egy rövid nevet a az alkalmazás regisztrációját a **neve** mezőbe. Alatt a **átirányítási URI-t (nem kötelező)** válassza **nyilvános ügyfél (mobil és asztali)** legördülő a bal oldalon, és adja meg `https://microsoft.com` be a szövegmezőbe, a jobb oldalon. Kattintson a **Register** (Regisztrálás) elemre.

    ![Hozzon létre panel](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Győződjön meg arról, hogy [az alkalmazás regisztrálva van egy *natív alkalmazás*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), nyissa meg a **hitelesítési** az alkalmazás regisztrációját, és a panelen görgessen lefelé a panelen. Az a **ügyféltípus alapértelmezett** válassza **Igen** a **kezelni alkalmazás nyilvános ügyfél**. 

    ![Alapértelmezett natív](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Nyissa meg a **áttekintése** a regisztrált alkalmazás panel és a egy ideiglenes fájlba a következő entitásokat értékeinek másolására. A mintaalkalmazás konfigurálását a következő szakaszokban ezeket az értékeket fogja használni.

    - **Az Alkalmazásazonosítót (ügyfél)**
    - **(Bérlő) címtár-azonosító**

    ![Az Azure Active Directory-alkalmazás azonosítója](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Nyissa meg a **API-engedélyek** az alkalmazás regisztrációját a panelen. Válassza ki **adjon hozzá egy engedélyt** gombra. Az a **kérelem API-engedélyek** panelen válassza a **API-k saját szervezete** lapra, és keressen rá **Azure intelligens tárolóhelyek**. Válassza ki a **Azure intelligens tárolóhelyek szolgáltatás** API-t.

    ![Keresési API](./media/digital-twins-permissions/aad-app-search-api.png)

1. A kijelölt API megjelenik-e **Azure digitális Twins** ugyanazon **kérelem API-engedélyek** ablaktáblán. Válassza ki a **olvasási (1)** legördülő lista, és válassza ki **Read.Write** jelölőnégyzetet. Válassza ki a **engedélyek hozzáadása** gombra.

    ![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. Szervezeti beállítások, attól függően szükség lehet, hogy további lépések szükségesek a oszthatnak ki adminisztrátori hozzáférést az API-ra. További részletekért forduljon a rendszergazda. A rendszergazdai hozzáférés jóváhagyása, miután a **rendszergazdai jóváhagyás szükséges** oszlopa a **API-engedélyek** ablaktáblán jelennek meg az API-k számára a következőhöz hasonló:

    ![API-engedélyek hozzáadása](./media/digital-twins-permissions/aad-app-admin-consent.png)

