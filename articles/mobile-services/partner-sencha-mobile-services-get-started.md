<properties
    pageTitle="Bevezetés az Azure Mobile Services és a Sencha használatába"
    description="Ez az oktatóanyag segítséget nyújt a fejlesztés első lépéseiben a Mobile Services és a Sencha HTML5 mobilalkalmazás-keretrendszer használatával."
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-sencha"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="02/10/2016"
    ms.author="glenga"/>

# <a name="getting-started"> </a>Bevezetés a Mobile Services és a Sencha Touch használatába

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Áttekintés

Az oktatóanyag bemutatja, hogyan aknázhatja ki az Azure Mobile Services előnyeit Sencha Touch-alkalmazásában. Egy egyszerű *Teendőlista* alkalmazást fog létrehozni a Sencha Touch használatával, amely alkalmazás a klasszikus Azure portálon meghatározott mobilszolgáltatást használja. Ez az oktatóanyag olyan tapasztalt és közepesen gyakorlott webalkalmazás-fejlesztőknek szól, akik jól ismerik a JavaScriptet és járatosak a Sencha Touch keretrendszer használatában.

Ha inkább a videót tekintené meg, ez a klip ugyanazokat a lépéseket tartalmazza, mint az oktatóanyag. A videóban Arthur Kay magyarázza el, hogyan készíthet Sencha Touch-alkalmazásokat az Azure Mobile Services Backend használatával.

> [AZURE.VIDEO getting-started-with-sencha-touch]


A kész alkalmazásról az alábbiakban látható egy képernyőkép:

![][0]

##Követelmények

- A [Sencha Touch](http://wwww.sencha.com/products/touch/download" target="_blank") letöltése és telepítése.

- A [Sencha parancssori eszköz](http://www.sencha.com/products/sencha-cmd/download" target="_blank") letöltése és telepítése.

- Java-futtatókörnyezet (JRE) vagy Java fejlesztői készlet (ha Android-alkalmazásokat hoz létre)
- Ruby és SASS gem.

## <a name="create-new-service"> </a>Új mobilszolgáltatás létrehozása

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

##A TodoItems tábla létrehozása

Miután létrehozta a mobilszolgáltatást, a klasszikus Azure portálon található egyszerű gyors üzembe helyezési szolgáltatással létrehozhat egy új adatbázistáblát a mobilszolgáltatásban való használatra.

1. A [klasszikus Azure portál] kattintson a **Mobile Services** elemre, majd az imént létrehozott mobilszolgáltatásra.

2. A gyors üzembe helyezés lap **Choose platform** (Platform kiválasztása) részében kattintson a **HTML** elemre, és bontsa ki a **Create a new HTML app** (Új HTML-alkalmazás létrehozása) részt.

    ![Mobil gyors üzembe helyezés – HTML](./media/partner-sencha-mobile-services-get-started/mobile-portal-quickstart-html.png)

    Ez megjeleníti a mobilszolgáltatáshoz kapcsolódó új HTML-alkalmazás létrehozásának és üzemeltetésének három egyszerű lépését.

    ![Mobil gyors üzembe helyezés – HTML](./media/partner-sencha-mobile-services-get-started/mobile-quickstart-steps-html.png)

3. Kattintson a **Create TodoItems table** (TodoItem tábla létrehozása) elemre az alkalmazásadatok tárolására szolgáló tábla létrehozásához.

    > [AZURE.NOTE] NE töltse le a HTML-alkalmazást a klasszikus Azure portálról. Ehelyett manuálisan létrehozunk egy Sencha Touch-alkalmazást a következő szakaszban.


1. Jegyezze fel az **appKey** és **appUrl** értékeket a klasszikus Azure portálon. Ezekre majd szüksége lesz az oktatóanyag egyéb szakaszaiban.

    ![alkalmazáskulcs](./media/partner-sencha-mobile-services-get-started/mobile-app-key-portal.png)

1. A **Configure** (Konfigurálás) lapon ellenőrizze, hogy a(z) `localhost` már szerepel-e a **Allow requests from host names** (Kérések engedélyezése az állomásnevekről) listában az **Cross-Origin Resource Sharing (CORS)** (Eltérő eredetű erőforrás-megosztás (CORS)) felületen. Ha nincs a listán írja be a(z) `localhost` szöveget a **Host Name** (Állomásnév) mezőbe, majd kattintson a **Save** (Mentés) gombra.

    ![A CORS beállítása helyi állomásként](./media/partner-sencha-mobile-services-get-started/mobile-services-set-cors-localhost.png)

##A Touch-alkalmazás létrehozása

A Sencha Touch-sablonalkalmazások létrehozása egyszerűen elvégezhető a Sencha parancssori eszközzel, és remek módja az alkalmazások gyors üzembe helyezésének.

A könyvtárban, ahová a Touch keretrendszert telepítette, adja ki a következő parancsot:

    $ sencha generate app Basic /path/to/application

Ez létrehoz egy Touch-sablonalkalmazást „Basic” alkalmazásnéven. Az alkalmazás indításához egyszerűen irányítsa a böngészőt az /útvonal/az/alkalmazáshoz helyre, és a rendszer visszaadja a standard Touch-mintaalkalmazást.

##A Sencha Touch Azure-bővítmények telepítése

Az Azure-bővítmény manuálisan vagy egy Sencha-csomag részeként telepíthető. Az alkalmazott módszer teljes mértékben Öntől függ.

###Manuális telepítés

A legtöbb Touch-alkalmazásban a külső osztálykönyvtárak hozzáadásához egyszerűen csak letölti a csomagot, kicsomagolja az alkalmazáskönyvtárba, és konfigurálja a könyvtár helyét a Touch-betöltőben.

Az Azure-bővítményeket manuálisan az alábbi lépésekkel adhatja hozzá az alkalmazáshoz:

1. Töltse le az Azure-bővítménycsomagot [innen](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure). (A hozzáféréshez használhatja a Sencha-fórumbeli azonosítóját.)

2. Másolja az Azure-bővítménycsomagot a letöltési könyvtárból a végső helyére, és csomagolja ki:

        $ cd /path/to/application
        $ mv /download-location/azure.zip .
        $ unzip azure.zip

    Ez létrehoz egy **azure** könyvtárat, amely a teljes csomag forrását, példákat és a dokumentációt tartalmazza. A forrás az **azure/src** könyvtárban lesz.


###Telepítés Sencha-csomagként

> [AZURE.NOTE] Ezt a módszert csak akkor használhatja, ha az alkalmazást a(z) <code>sencha generate app</code> parancs használatával hozta létre.

A Sencha parancssori eszközzel létrehozott minden alkalmazás rendelkezik egy „packages” mappával a gyökérkönyvtárban. Ennek a mappának a helye konfigurálható, azonban a „packages” mappa szerepköre helytől függetlenül az, hogy az alkalmazás (vagy alkalmazások, ha egy Sencha-munkaterületet hozott létre) által használt összes csomagot tárolja.

Mivel az Ext.Azure a Sencha parancssori eszköz „csomagja”, a forráskód könnyen telepíthető és foglalható bele az alkalmazásba a Sencha parancssori eszköz használatával. (További információért lásd: [Sencha parancssori eszköz csomagjai](http://docs.sencha.com/cmd/6.x/cmd_packages/cmd_packages.html)).

Ahhoz, hogy le tudja tölteni és telepíteni tudja az Azure-bővítménycsomagot a Sencha-csomagok tárházából, a csomag nevét hozzá kell adnia az **app.json** fájlhoz, és fel kell építenie az alkalmazást:

1. Adja hozzá az Azure-csomagot az app.json fájl szükséges szakaszához:

        {
            "name": "Basic",
            "requires": [
                "touch-azure"
            ]
        }

2. Építse újra az alkalmazást a **sencha parancssori eszközzel** a csomag beolvasásához és telepítéséhez:

        $ sencha app build

Most a **sencha app build** és a **sencha app refresh** is végrehajtja szükséges lépéseket a csomag az alkalmazásba való importálásához. A csomagkövetelmények módosítása után jellemzően futtatnia kell a **sencha app refresh** parancsot, hogy a „fejlesztői mód” támogatásához szükséges metaadatok naprakészek legyenek.

A Sencha parancssori eszköz a futtatott parancstól függetlenül letölti és kicsomagolja a csomagot a „packages” mappába. Ezután egy „packages/touch-azure” mappát talál majd a munkaterületen.

##Az Azure belefoglalása és konfigurálása

**Fájlnév**: app.js

Miután letöltötte és telepítette az Azure-bővítményt az alkalmazás könyvtárába, a következő lépés, hogy megadja a forrásfájlok helyét az alkalmazásnak, valamint hogy kötelezőként állítja be őket:

1. A forráskód helyének konfigurálása a Sencha-betöltőben:

        Ext.Loader.setConfig({
            enabled : true,
            paths   : {
                'Ext'       : 'touch/src',
                'Ext.azure' : '/path-to/azure-for-touch/azure/src'
            }
        });


2. Az Azure-osztályfájlok kötelezőként való beállítása:

        Ext.application({

            requires: [ 'Ext.azure.Azure' ],

            // ...

        });


3. Az Azure konfigurálása

    Az Azure-csomag az **Ext.Azure.init** metódus meghívásával inicializálható az alkalmazás indítási szakaszában. A metódus kap egy konfigurációs objektumot, amely a mobilszolgáltatás hitelesítő adatai mellett egyéb használni kívánt hitelesítő adatokat és szolgáltatásokat is tartalmaz.

    Bár a konfigurációs objektum közvetlenül is átadható az inicializálási metódusnak, javasoljuk, hogy hozzon létre egy Sencha alkalmazáskonfigurációs tulajdonságot **azure** néven, és ebben helyezze el az összes vonatkozó tulajdonságot. Ezután ezt a tulajdonságértéket adhatja át az Ext.Azure.init metódusnak.

    Amikor létrehoz egy mobilszolgáltatást az Azure-ban (lásd: [Ismerkedés az Azure-ral](http://senchaazuredocs.azurewebsites.net/#!/guide/getting_started)), a rendszer egy alkalmazáskulcsot és egy URL-címet rendel az adott szolgáltatáshoz. Ezt az információt meg kell adni az Azure-csomagnak, hogy csatlakozni tudjon a szolgáltatáshoz.

    Ez a példa egy nagyon egyszerű Azure-konfigurációt és inicializálást mutat be, amelyben csak az alkalmazáskulcs és az URL-cím van megadva:

        Ext.application({
            name: 'Basic',

            requires: [ 'Ext.azure.Azure' ],

            azure: {
                appKey: 'myazureservice-access-key',
                appUrl: 'myazure-service.azure-mobile.net'
            },

            launch: function() {

                // Call Azure initialization

                Ext.Azure.init(this.config.azure);

           }
        });

    Az Azure konfigurációs lehetőségeivel kapcsolatos további információkért tekintse át az Ext.Azure API dokumentációját.


Gratulálunk! Az alkalmazás most már hozzáféréssel rendelkezik a mobilszolgáltatáshoz.

##A Teendőlista alkalmazás felépítése

Az alkalmazás konfigurálásával belefoglaltuk az Azure-bővítményt, és megadtuk a mobilszolgáltatás hitelesítő adatait. A következő lépésben létrehozhatunk egy Touch-alkalmazást, amely a mobilszolgáltatás használatával a Teendőlista a szolgáltatásban tárolt adatainak megtekintésére és szerkesztésére használható.

###Az Azure-adatproxy konfigurálása

**Fájlnév**: app/model/TodoItem.js

A Touch alkalmazás egy adatproxyn keresztül kommunikál majd a mobilszolgáltatással. A proxy küldi el a kéréseket a mobilszolgáltatásnak, és fogadja az onnan érkező adatokat. Egy Touch-adatmodellel és -adattárral együtt a Touch átveszi és elvégzi a távoli adatok feldolgozásának és az alkalmazásba való beolvasásának bonyolult feladatát.

A Sencha Touch-modellek biztosítják az alkalmazásban használt adatrekordok definícióit. Ezek segítségével nemcsak az adatmezők definiálhatók, hanem az alkalmazás és az Azure mobilszolgáltatás közötti kommunikációt végző proxy konfigurációja is megadható.

Az alábbi kódban látható, hogy meghatároztuk a modell mezőit (és a típusukat), valamint megadtuk a proxy konfigurációját is. A proxy konfigurálásakor hozzá kell rendelnie egy típust (esetünkben ez az „azure”), a mobilszolgáltatás táblanevét (ToDoItem) és egyéb választható paramétereket. A példában bekapcsoljuk a proxy lapozását, így zökkenőmentesen lapozhatunk előre és hátra a listaelemek között.

Az Azure proxy automatikusan beállítja az összes HTTP-fejlécet az Azure API által várt megfelelő CRUD-műveletekkel (beleértve a hitelesítő adatokat is, ha vannak).

    Ext.define('Basic.model.TodoItem', {
        extend : 'Ext.data.Model',

        requires : [
            'Ext.azure.Proxy'
        ],

        config : {
            idProperty : 'id',
            useCache   : false,

            fields     : [
                {
                    name : 'id',
                    type : 'int'
                },
                {
                    name : 'text',
                    type : 'string'
                },
                {
                    name : 'complete',
                    type : 'boolean'
                }
            ],

            proxy : {
                type               : 'azure',
                tableName          : 'TodoItem',
                enablePagingParams : true
            }
        }
    });


###A Teendőlista elemeinek tárolása

**Fájlnév**: app/store/TodoItems.js

A Sencha Touch tárolókat adatrekord-gyűjtemények (modellek) tárolására szokták használni, amelyek a Touch-komponensek forrásaiként a rekordok számos különféle módon való megjelenítésére használhatók. Ezek többek között rácsok, diagramok és listák lehetnek.

Itt egy olyan tárolót definiálunk, amely a Teendőlista az Azure-mobilszolgáltatásból lekért összes tárolt elemét tartalmazza majd. Figyelje meg, hogy a tároló konfiguráció tartalmazza a modelltípus nevét (Basic.model.TodoItem – fentebb definiálva). Ez meghatározza a tárolóban majd tárolt rekordok szerkezetét.

Léteznek további konfigurációs beállítások is a tárolóhoz, amelyek például megadják az oldalméretet (8 rekord) vagy azt, hogy a tárolóban lévő rekordokat az Azure-mobilszolgáltatás rendezze távolról (a tárolóban helyi rendezés nem történik).

    Ext.define('Basic.store.TodoItems', {
        extend : 'Ext.data.Store',

        requires : [
            'Basic.model.TodoItem'
        ],

        config : {
            model        : 'Basic.model.TodoItem',
            pageSize     : 8,
            remoteSort   : true,
            remoteFilter : true
        }
    });


###A Teendőlista elemeinek megtekintése és szerkesztése

**Fájlnév**: app/view/DataItem.js

Most, hogy meghatároztuk a Teendőlista egyes elemeinek szerkezetét, és létrehoztunk egy tárolót az összes rekord tárolására, meg kell fontolnunk, hogyan jelenítsük meg ezeket az információkat az alkalmazás felhasználóinak. Az információkat rendszerint **Nézetek** használatával jelenítjük meg a felhasználóknak. A nézetek egy vagy több, egymagában vagy másokkal kombinált Touch-összetevőből állhatnak.

Az alábbi nézet egy ListItem elemből áll, amely meghatározza, hogy az egyes rekordok hogyan jelenjenek meg, valamint néhány gombból, amelyek az egyes tételek törlésére vonatkozó műveleteket vezérlik.

    Ext.define('Basic.view.DataItem', {
        extend : 'Ext.dataview.component.ListItem',
        xtype  : 'basic-dataitem',

        requires : [
            'Ext.Button',
            'Ext.layout.HBox',
            'Ext.field.Checkbox'
        ],

        config : {
            checkbox : {
                docked     : 'left',
                xtype      : 'checkboxfield',
                width      : 50,
                labelWidth : 0
            },

            text : {
                flex : 1
            },

            button : {
                docked   : 'right',
                xtype    : 'button',
                ui       : 'plain',
                iconMask : true,
                iconCls  : 'delete',
                style    : 'color: red;'
            },

            dataMap : {
                getText : {
                    setHtml : 'text'
                },

                getCheckbox : {
                    setChecked : 'complete'
                }
            },

            layout : {
                type : 'hbox',
                align: 'stretch'
            }
        },

        applyCheckbox : function(config) {
            return Ext.factory(config, Ext.field.Checkbox, this.getCheckbox());
        },

        updateCheckbox : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        },

        applyButton : function(config) {
            return Ext.factory(config, Ext.Button, this.getButton());
        },

        updateButton : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        }

    });


###Az elsődleges nézet létrehozása

**Fájlnév**: app/view/Main.js

Most, hogy definiáltuk a Teendőlista egyes elemeinek elrendezését (fent), egy teljes felhasználói felületet szeretnénk építeni lista köré, amely a tényleges elemlista, az alkalmazáscím és az új feladatok felvételére szolgáló gomb megjelenését határozza meg.

    Ext.define('Basic.view.Main', {
        extend : 'Ext.dataview.List',
        xtype  : 'main',

        requires : [
            'Ext.TitleBar',
            'Ext.dataview.List',
            'Ext.data.Store',
            'Ext.plugin.PullRefresh',
            'Ext.plugin.ListPaging',
            'Basic.view.DataItem'
        ],

        config : {
            store : 'TodoItems',

            useSimpleItems : false,
            defaultType    : 'basic-dataitem',

            plugins : [
                {
                    xclass          : 'Ext.plugin.PullRefresh',
                    pullRefreshText : 'Pull down to refresh!'
                },
                {
                    xclass     : 'Ext.plugin.ListPaging',
                    autoPaging : true
                }
            ],

            scrollable : {
                direction     : 'vertical',
                directionLock : true
            },

            items : [
                {
                    docked : 'top',
                    xtype  : 'titlebar',
                    title  : 'Azure Mobile - Basic Data Example'
                },
                {
                    xtype  : 'toolbar',
                    docked : 'bottom',
                    items  : [
                        {
                            xtype       : 'textfield',
                            placeHolder : 'Enter new task',
                            flex        : 1
                        },
                        {
                            xtype  : 'button',
                            action : 'add',
                            text   : 'Add'
                        }
                    ]
                }
            ]
        }
    });

###Az elemek együttműködésének biztosítása

**Fájlnév**: app/controller/Main.js

Az alkalmazás működésének utolsó lépését a gombnyomásokra adott válaszok (törlés, mentés stb.), valamint a kérések mögötti logika biztosítása képezik. A Sencha Touch ehhez vezérlőket használ, amelyek figyelik ezeket az eseményeket, és megfelelően válaszolnak.

    Ext.define('Basic.controller.Main', {
        extend : 'Ext.app.Controller',

        config : {
            refs : {
                todoField : 'main toolbar textfield',
                main      : 'main'
            },

            control : {
                'button[action=add]'    : {
                    tap : 'onAddItem'
                },
                'button[action=reload]' : {
                    tap : 'onReload'
                },

                main : {
                    activate      : 'loadInitialData',
                    itemdoubletap : 'onItemEdit'
                },

                'basic-dataitem checkboxfield' : {
                    change : 'onItemCompleteTap'
                },

                'basic-dataitem button' : {
                    tap : 'onItemDeleteTap'
                }
            }
        },

        loadInitialData : function () {
            Ext.getStore('TodoItems').load();
        },

        onItemDeleteTap : function (button, e, eOpts) {
            var store    = Ext.getStore('TodoItems'),
                dataItem = button.up('dataitem'),
                rec      = dataItem.getRecord();

            rec.erase({
                success: function (rec, operation) {
                    store.remove(rec);
                },
                failure: function (rec, operation) {
                    Ext.Msg.alert(
                        'Error',
                        Ext.util.Format.format('There was an error deleting this task.<br/><br/>    Status Code: {0}<br/>Status Text: {1}',
                        operation.error.status,
                        operation.error.statusText)
                    );
                }
            });
        },

        onItemCompleteTap : function (checkbox, newVal, oldVal, eOpts) {
            var dataItem = checkbox.up('dataitem'),
                rec      = dataItem.getRecord(),
                recVal   = rec.get('complete');

            // this check is needed to prevent an issue where multiple creates get triggered from one create
            if (newVal !== recVal) {
                rec.set('complete', newVal);
                rec.save({
                    success: function (rec, operation) {
                        rec.commit();
                    },
                    failure: function (rec, operation) {
                        // since there was a failure doing the update on the server then silently reject the change
                        rec.reject(true);
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                            operation.error.status,
                            operation.error.statusText)
                        );
                    }
                });
            }
        },

        onItemEdit : function (list, index, target, record, e, eOpts) {
            var rec = list.getSelection()[0];

            Ext.Msg.prompt('Edit', 'Rename task',
                function (buttonId, value) {
                    if (buttonId === 'ok') {
                        rec.set('text', value);
                        rec.save({
                            success: function (rec, operation) {
                                rec.commit();
                            },
                            failure: function (rec, operation) {
                                // since there was a failure doing the update on the server then reject the change
                                rec.reject();
                                Ext.Msg.alert(
                                    'Error',
                                    Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                                    operation.error.status,
                                    operation.error.statusText)
                                );
                            }
                        });
                    }
                },
                null,
                false,
                record.get('text')
            );
        },

        onReload : function () {
            Ext.getStore('TodoItems').load();
        },

        onAddItem : function () {
            var me = this,
                rec,
                store = Ext.getStore('TodoItems'),
                field = me.getTodoField(),
                value = field.getValue();

            if (value === '') {
                Ext.Msg.alert('Error', 'Please enter Task name', Ext.emptyFn);
            }
            else {
                rec = Ext.create('Basic.model.TodoItem', {
                    complete : false,
                    text     : value
                });
                //store.insert(0, rec); //insert at the top
                //store.sync();
                rec.save({
                    success: function (rec, operation) {
                        store.insert(0, rec); //insert at the top
                        field.setValue('');
                    },
                    failure: function (rec, operation) {
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error creating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                            operation.error.status,
                            operation.error.statusText)
                        );
                    }
                });
            }
        }
    });

###Az alkalmazás összeállítása

**Fájlnév**: app.js

Az utolsó lépés a fő alkalmazásfájl szerkesztésének befejezése, és a definiált modellekkel, tárolókkal, nézetekkel és vezérlőkkel kapcsolatos információk megadása. Az ezekhez az erőforrásokhoz tartozó forrásfájlok automatikusan betöltődnek az alkalmazásba. Végül meghívjuk az indítási metódust, amely létrehozza és megjeleníti az alkalmazás elsődleges nézetét („Basic.main.View”).


    Ext.Loader.setConfig({
        enabled : true,
        paths   : {
            'Ext'       : 'touch/src',
            'Ext.azure' : 'packages/azure/src'
        }
    });

    Ext.application({
        name : 'Basic',

        requires : [
            'Ext.MessageBox',
            'Ext.azure.Azure'
        ],

        views : [
            'Main'
        ],

        controllers : [
            'Main'
        ],

        stores : [
            'TodoItems'
        ],

        azure : {
            appUrl : 'YOUR_APP_URL.azure-mobile.net',
            appKey : 'YOUR_APP_KEY'
        },

        icon : {
            '57'  : 'resources/icons/Icon.png',
            '72'  : 'resources/icons/Icon~ipad.png',
            '114' : 'resources/icons/Icon@2x.png',
            '144' : 'resources/icons/Icon~ipad@2x.png'
        },

        isIconPrecomposed : true,

        startupImage : {
            '320x460'   : 'resources/startup/320x460.jpg',
            '640x920'   : 'resources/startup/640x920.png',
            '768x1004'  : 'resources/startup/768x1004.png',
            '748x1024'  : 'resources/startup/748x1024.png',
            '1536x2008' : 'resources/startup/1536x2008.png',
            '1496x2048' : 'resources/startup/1496x2048.png'
        },

        launch : function () {
            // Destroy the #appLoadingIndicator element
            Ext.fly('appLoadingIndicator').destroy();

            // Initialize Azure
            Ext.Azure.init(this.config.azure);

            // Initialize the main view
            Ext.Viewport.add(Ext.create('Basic.view.Main'));
        },

        onUpdated : function () {
            Ext.Msg.confirm(
                "Application Update",
                "This application has just successfully been updated to the latest version. Reload now?",
                function (buttonId) {
                    if (buttonId === 'yes') {
                        window.location.reload();
                    }
                }
            );
        }
    });

###A Sencha Touch-alkalmazás üzemeltetése és futtatása

Az oktatóanyag utolsó szakaszát egy új alkalmazás a helyi számítógépen való üzemeltetése és futtatása teszi ki.

  1. A terminálon tallózással keresse meg a tömörítetlen alkalmazás helyét.

  2. A Sencha parancssori eszközzel futtassa az alábbi parancsokat:

    * *sencha app refresh*: Ez a parancs utasítja a Sencha parancssori eszközt, hogy keresse meg az alkalmazás összes függőségét, és töltse le a szükséges csomagokat (például a [Sencha Touch Azure-bővítményeket](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)).

    * *sencha web start*: Ez a parancs elindít egy helyi webkiszolgálót az alkalmazás teszteléséhez.

    ![sencha web start](./media/partner-sencha-mobile-services-get-started/sencha-web-start.png)

  3. Nyissa meg a terminálon listázott URL-címet egy webböngészőben az alkalmazás indításához (például http://localhost:1841).

  4. Az alkalmazásban írjon be egy jelentéssel bíró szöveget, például „Az oktatóanyag befejezése”, majd kattintson az **Add** (Hozzáadás) gombra.

    ![új teendő elem](./media/partner-sencha-mobile-services-get-started/new-todo-item.png)

    Ez egy POST kérést küld az Azure-ban futtatott új mobilszolgáltatásnak. A kérelem adatai beillesztésre kerülnek a TodoItem táblába.

  5. A [klasszikus Azure portál] visszatérve kattintson a **Data** (Adatok) fülre, majd a TodoItems táblára.

    ![Teendő elemek táblája](./media/partner-sencha-mobile-services-get-started/mobile-data-tab.png)

    Így tallózással kiválaszthatja az alkalmazás által a táblába beszúrt adatokat.

    ![teendők tábla tallózása](./media/partner-sencha-mobile-services-get-started/mobile-data-browse.png)

##Következő lépések
Most, hogy végzett az első lépésekről szóló útmutatóval, megtudhatja, hogyan hajthat végre további fontos feladatokat a Mobile Servicesben a Sencha használatával.

[Letölthet](https://github.com/arthurakay/sencha-touch-azure-example) egy további stíluselemekkel és szolgáltatásokkal rendelkező kész mintaalkalmazást, amelyből láthatja, mi mindenre képes még a Sencha Touch!

Ezután alaposabban tanulmányozhatja a Sencha Touch Azure-bővítményekkel kapcsolatos információkat:

  * Mintaalkalmazás [bemutatása](http://docs.sencha.com/touch-azure/1.0.0/#!/guide/data_filters)
  * Segítség kérése a [Sencha fórumán](http://www.sencha.com/forum)
  * A [Sencha dokumentációjának](http://docs.sencha.com/) böngészése
  * Using Sencha With Azure Mobile Services (A Sencha és az Azure Mobile Services használata): [(videó)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-126-Using-Sencha-With-Windows-Azure-Mobile-Services)


##További források

  * [A Sencha Touch letöltése](http://pages.sencha.com/touch-for-azure.html)
  * [Sencha Touch Azure-bővítmények](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)


##Összefoglalás

Az itt vázolt példa a Sencha Touch Azure-bővítménycsomag részét képezi, és a példák könyvtárában az alapszintű adatok példájaként található. A példakönyvtár más példákat is tartalmaz, amelyek a bővítmény egyéb funkcióit mutatják be részletes megjegyzésekkel és magyarázatokkal.

A Sencha Touch használatának első lépéseivel kapcsolatos részletesebb információkért tekintse meg az [útmutatók](http://docs.sencha.com/touch/#!/guide) teljes gyűjteményét


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- images -->
[0]: ./media/partner-sencha-mobile-services-get-started/finished-app.png

[klasszikus Azure portál]: https://manage.windowsazure.com/


<!--HONumber=Jun16_HO2-->


