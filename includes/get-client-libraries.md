### <a name="install-via-composer"></a>Keresztül szerkesztő telepítése
1. Hozzon létre egy fájlt **composer.json** a projekt gyökérkönyvtárában, és az alábbi kód felvétele:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Töltse le  **[composer.phar] [ composer-phar]**  a projekt gyökérkönyvtárában.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökérkönyvtárában
   
    ```
    php composer.phar install
    ```

Másik lehetőségként keresse fel a [Azure Storage PHP ügyféloldali kódtár] [ php-sdk-github] a Githubon a forráskód klónozását.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
