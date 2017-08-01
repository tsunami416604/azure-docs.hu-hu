---
title: "Java API-alkalmazás buildjének elkészítése és telepítése az Azure App Service platformon"
description: "Megtudhatja, hogyan hozhat létre egy Java API-alkalmazáscsomagot, és hogyan telepítheti az Azure App Service platformra."
services: app-service\api
documentationcenter: java
author: rmcmurray
manager: erikre
editor: tdykstra
ms.assetid: 8d21ba5f-fc57-4269-bc8f-2fcab936ec22
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: get-started-article
ms.date: 04/25/2017
ms.author: rachelap;robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 26011d72644f8b13a711d847d83175ce384f2053
ms.contentlocale: hu-hu
ms.lasthandoff: 06/17/2017


---
# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Java API-alkalmazás buildjének elkészítése és telepítése az Azure App Service platformon
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Java-alkalmazást, és hogyan telepítheti az Azure App Service API Apps szolgáltatásba a [Git] használatával. Az oktatóanyagban lévő utasítások bármilyen, Java-programok futtatására alkalmas operációs rendszeren végrehajthatók. Az oktatóanyagban szereplő kód a [Maven] használatával lett lefordítva. A RESTful szolgáltatás létrehozásához a [Jax-RS] API-t használjuk, és a szolgáltatás generálása [Swagger] metaadat-specifikáció alapján történik, amelyet a [Swagger Editor] programmal készítünk el.

## <a name="prerequisites"></a>Előfeltételek
1. [Java Developer's Kit 8] \(vagy újabb)
2. A fejlesztői gépen telepítve van a [Maven]
3. A fejlesztő gépen telepítve van a [Git]
4. [Ingyenes] vagy fizetős [Microsoft Azure]-előfizetés
5. HTTP-tesztalkalmazás, például [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Az API strukturálása a Swagger.IO használatával
A swagger.io online szerkesztő segítségével az API struktúráját leírhatja Swagger JSON- vagy YAM- kóddal. Az API felületének megtervezése után a kódot exportálhatja különböző platformokra és keretrendszerekre. A következő szakaszban a generált kódot úgy módosítjuk, hogy tartalmazza a funkciók utánzatait. 

A demonstráció elején a swagger.io online szerkesztőbe Swagger JSON-kódot illesztünk be, amelyből a JAX-RS segítségével egy REST API-végpont elérésére alkalmas kódot generálunk. Ezt követően a generált kódot úgy szerkesztjük, hogy próbaadatokat adjon vissza, ezáltal egy adatperzisztencia-mechanizmus feletti REST API-t szimulálva.  

1. Másolja a vágólapra a következő Swagger JSON-kódot:
   
        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }
2. Nyissa meg az [Online Swagger Editort]. A szerkesztőben kattintson a **File -> Paste JSON**(Fájl -> JSON-kód beillesztése)  menüpontra.
   
    ![A JSON beillesztése menüpont][paste-json]
3. Illessze be a Contacts List API korábban vágólapra másolt Swagger JSON-kódját. 
   
    ![JSON-kód beillesztése a Swaggerbe][pasted-swagger]
4. Olvassa el a szerkesztőben renderelt dokumentációs oldalakat és API-összefoglalót. 
   
    ![A Swagger által generált dokumentumok megtekintése][view-swagger-generated-docs]
5. A **Generate Server -> JAX-RS** (Kiszolgáló generálása -> JAX-RS) menüpontot választva generálja le a kiszolgálóoldali kódot, amelyet szerkeszteni fog, és próbaimplementációval fog bővíteni. 
   
    ![A Kód generálása menüpont][generate-code-menu-item]
   
    A kódgenerálás után egy ZIP-fájlt tölthet le. Ez a fájl tartalmazza a Swagger kódgeneráló által generált kódot és az összes kapcsolódó fordítási parancsprogramot. Bontsa ki a fájl teljes tartalmát a fejlesztő munkaállomás valamelyik könyvtárába. 

## <a name="edit-the-code-to-add-api-implementation"></a>A kód szerkesztése API-implementáció hozzáadásához
Ebben a szakaszban a Swagger által generált kód kiszolgálóoldali implementációját a saját kódjára cseréli le. Az új kód egy Contact entitásokat tartalmazó ArrayListet fog visszaadni a hívó ügyfélnek. 

1. Nyissa meg az *src/gen/java/io/swagger/model* mappában található  *Contact.java* modellfájlt a [Visual Studio Code]-dal vagy kedvenc szövegszerkesztőjével. 
   
    ![A Contact modellfájl megnyitása][open-contact-model-file]
2. Adja hozzá az alábbi konstruktort a **Contact** osztályon belül. 
   
        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }
3. Nyissa meg az *src/main/java/io/swagger/api/impl* mappában található *ContactsApiServiceImpl.java* szolgáltatásimplementációs fájlt a [Visual Studio Code]-dal vagy kedvenc szövegszerkesztőjével.
   
    ![A Contact szolgáltatásimplementációs fájl megnyitása][open-contact-service-code-file]
4. Írja felül a fájlban lévő kódot az alábbi új kóddal, amely a szolgáltatáskód próbaimplementációját tartalmazza. 
   
        package io.swagger.api.impl;
   
        import io.swagger.api.*;
        
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
               
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;
   
        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
   
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
   
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
   
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
   
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }
5. Nyisson meg egy parancssort, és váltson az alkalmazás gyökérkönyvtárára.
6. Hajtsa végre a következő Maven-parancsot a kód lefordításához és a Jetty alkalmazáskiszolgálóval való helyi futtatásához. 
   
        mvn package jetty:run
7. A parancssori ablakban azt kell látnia, hogy a Jetty elindította a kódot a 8080-as porton. 
   
    ![A Contact szolgáltatásimplementációs fájl megnyitása][run-jetty-war]
8. A [Postman] használatával indítson egy lekérdezést a „get all contacts” API-metódusra a http://localhost:8080/api/contacts címen.
   
    ![A Contacts API meghívása][calling-contacts-api]
9. A [Postman] használatával indítson egy lekérdezést a „get specific contact” API-metódusra a http://localhost:8080/api/contacts/2 címen.
   
    ![A Contacts API meghívása][calling-specific-contact-api]
10. Végezetül fordítsa le a Java WAR (Web ARchive) fájlt a következő Maven-parancsnak a konzolban való futtatásával. 
    
         mvn package war:war
11. A WAR-fájl fordítás után bekerül a **target** mappába. Nyissa meg a **target** mappát, és nevezze át a WAR-fájlt **ROOT.war**-ra. (Ügyeljen a kis- és nagybetűkre).
    
          rename swagger-jaxrs-server-1.0.0.war ROOT.war
12. Végezetül a következő parancsokat az alkalmazás gyökérmappájában végrehajtva hozza létre a **deploy** mappát a WAR-fájlnak az Azure platformra való telepítéséhez. 
    
          mkdir deploy
          mkdir deploy\webapps
          copy target\ROOT.war deploy\webapps
          cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>A kimenet közzététele az Azure App Service platformon
Ebben a szakaszban megtudhatja, hogyan hozhat létre új API-alkalmazást az Azure portálon, hogyan készítheti elő az API-alkalmazást Java-alkalmazások futtatására, valamint hogy hogyan telepítheti az újonnan létrehozott WAR-fájlt az Azure App Service platformra az új API-alkalmazás futtatásához. 

1. Hozzon létre egy új API-alkalmazást az [Azure Portal]. Ehhez kattintson a **Új -> Web + mobil -> API-alkalmazás** menüpontra, írja be az adatokat, majd kattintson a **Létrehozás** gombra.
   
    ![Új API-alkalmazás létrehozása][create-api-app]
2. Az API-alkalmazás létrehozása után nyissa meg az alkalmazás **Beállítások**  paneljét, majd kattintson az **Alkalmazás beállításai** menüpontra. Az elérhető lehetőségek közül válassza a legújabb Java-verziót, majd a legújabb Tomcat-verziót a **Webes tároló** menüből, és kattintson a **Mentés** gombra.
   
    ![A Java beállítása az API-alkalmazás paneljén][set-up-java]
3. Kattintson a **Telepítési hitelesítő adatok** menüpontra, és írja be azt a felhasználónevet és jelszót, amelyet a fájloknak az API-alkalmazásban való közzétételéhez szeretne használni. 
   
    ![Telepítési hitelesítő adatok beállítása][deployment-credentials]
4. Kattintson a **Központi telepítés forrása** menüpontra. Ezután kattintson a **Forrás választása** gombra, válassza a **Helyi git-tárház** lehetőséget, majd kattintson az **OK** gombra. Ezzel létrehoz egy Git-tárházat, amely az Azure-ban fut és az Ön API-alkalmazásához van társítva. Minden alkalommal, amikor kódot véglegesít a Git-tárház *master* ágán, a kódot közzéteszi a környezet az élő futó API-alkalmazáspéldányban. 
   
    ![Új helyi Git-tárház beállítása][select-git-repo]
5. Másolja az új Git-tárház URL-címét a vágólapra. Mentse, mert hamarosan szüksége lesz rá. 
   
    ![Új Git-tárház beállítása az alkalmazáshoz][copy-git-repo-url]
6. A Git push parancsával küldje el a WAR-fájlt az online tárházba. Ehhez nyissa meg a korábban létrehozott **deploy** mappát, hogy könnyen véglegesíthesse a kódot az App Service-ben futó tárházba. Ha már a konzolablakban van, és belépett a webapps mappát tartalmazó mappába, adja ki a következő Git-parancsokat a folyamat elindításához és a telepítés megkezdéséhez. 
   
         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master
   
    A **push** parancs kiadása után meg kell adnia azt a jelszót, amelyet a telepítési hitelesítő adatokhoz korábban megadott. Miután megadta a hitelesítő adatait, a portál értesíti arról, hogy a frissítés telepítése megtörtént.
7. Ha a Postmannel ismét eléri az Azure App Service-ben futó, újonnan telepített  API-alkalmazást, azt fogja tapasztalni, hogy az API viselkedése konzisztens, és most már a várt módon adja vissza a névjegyadatokat, és a Swagger.io által generált Java-kódot használja. 
   
    ![A Java Contacts REST API használata élőben az Azure-ban][postman-calling-azure-contacts]

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben egy Swagger JSON-fájllal és a Swagger.io szerkesztő által generált Java-kóddal indultunk el. Ezekből egyszerű módosításokkal és a Git telepítési folyamatának eredményeként egy működő, Java nyelven írt API-alkalmazást kaptunk. A következő oktatóanyag azt mutatja be, hogyan [használhat API-alkalmazásokat JavaScript-ügyfelekkel a CORS segítségével][App Service API CORS]. A sorozat későbbi részei bemutatják, hogyan implementálhatja a hitelesítést és az engedélyezést.

Erre a példára építve részletesen megismerheti, hogyan használhatja a [Storage SDK for Java] csomagot a JSON-blobok megtartásához. Vagy a [Document DB Java SDK] használatával mentheti a névjegyadatokat az Azure Document DB rendszerbe. 

<a name="see-also"></a>

## <a name="see-also"></a>Lásd még:
Az Azure Javával való használatáról további információ: [Azure Java fejlesztői központ].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Azure Portal]: https://portal.azure.com/
[Document DB Java SDK]: ../documentdb/documentdb-java-application.md
[Ingyenes]: https://azure.microsoft.com/pricing/free-trial/
[Git]: http://www.git-scm.com/
[Azure Java fejlesztői központ]: /develop/java/
[Java Developer's Kit 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax-RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Online Swagger Editort]: http://editor2.swagger.io/
[Postman]: https://www.getpostman.com/
[Storage SDK for Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Swagger Editor]: http://editor.swagger.io/
[Visual Studio Code]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png

