# xdebug php

### Xampp on Linux (Ubuntu)

1. [https://xdebug.org/wizard](https://xdebug.org/wizard)

2. Copy source from phpinfo().

3. Download xdebug.

4. Open terminal and goto xdebug folder.

5. `tar -xvzf xdebug-*.tgz`

6. `cd xdebug-2.6.1`

7. `sudo apt-get install autoconf`

8. `sudo /opt/lampp/bin/phpize`

9. `sudo ./configure --with-php-config=/opt/lampp/bin/php-config`

10. `sudo make`

11. `sudo cp modules/xdebug.so /opt/lampp/lib/php/extensions/no-debug-non-zts-{version}/`

12. Edit /opt/lampp/etc/php.ini 

    ```
        [zend]
        zend_extension="/opt/lampp/lib/php/extensions/no-debug-non-zts-{version}/xdebug.so"
        [xdebug]
        xdebug.remote_enable=1
        xdebug.remote_autostart = 1
        xdebug.remote_host=localhost
        xdebug.remote_port=9000
        xdebug.remote_handler=dbgp
    ```
13. `sudo /opt/lampp/lampp restart`

14. Check phpinfo().