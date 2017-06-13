# Jak poradzić sobie z oprogramowaniem Shoper.pl

Uwagi na podstawie wieloletniej pracy na oprogramowaniu Shoper.pl. Jest to system stabilny, jednak bez cache'owania powolny i jakakolwiek jego rozbudowa nie wchodzi w grę. 

Wady:
* rozbudowane API, które jednak działa bardzo powoli i w efekcie nie nadaje się do użytku
* aby postawić kopię sklepu do testów należy wykupić kolejną licencję

Rozbudowa
* są dostępne dodatki jednak opierają się o API Shopera, co powoduje że są w większości bezużyteczne aby klient z tego korzystał

Poniższe porady dotyczą licencji samodzielnej. Zmiany w kodzie mogą uniemożliwić przeprowadzenie aktualizacji. Róbmy kopię zapasową każdego modyfikowanego pliku.

## Odnośnik w stopce do Shopera
Zgodnie z licencją, w stopce musi być link do shoper.pl, jednak może on być z 'rel="nofollow"' oraz w innym miejscu niż dół strony.

Plik: plugins/plugins/Shoper/Event/Core.php

Funkcja smartyRenderFinished. Linie 8-25.

Oryginalnie:
```php
	public function smartyRenderFinished(stdClass $obj){
        if('default' == Zend_Controller_Front::getInstance()->getRequest()->getModuleName() && !Zend_Controller_Front::getInstance()->getRequest()->isXmlHttpRequest()) {
            if(preg_match('/<body([^>]*)>\s+<div\s+class="container"([^>]*)>/i', $obj->data)) {
                $pos = strrpos($obj->data, '</div>');
                $str = base64_decode($this->_bfooter);
                $obj->data = substr($obj->data, 0, $pos) . $str . substr($obj->data, $pos);
            } else {
                $obj->data = str_replace('</body>', base64_decode($this->_bfooter) . '</body>', $obj->data);
            }
        }

        if('mobile' == Zend_Controller_Front::getInstance()->getRequest()->getModuleName() && !Zend_Controller_Front::getInstance()->getRequest()->isXmlHttpRequest()) {
            $pos = strrpos($obj->data, '</div>');
            $pos = strrpos($obj->data, '</div>', $pos - strlen($obj->data) - 1);
            $str = base64_decode($this->_mfooter);
            $obj->data = substr($obj->data, 0, $pos) . $str . substr($obj->data, $pos);
        }
    }
```

Po zmianach:
```php
    public function smartyRenderFinished(stdClass $obj){
        if('default' == Zend_Controller_Front::getInstance()->getRequest()->getModuleName() && !Zend_Controller_Front::getInstance()->getRequest()->isXmlHttpRequest()) {
            if(preg_match('/<body([^>]*)>\s+<div\s+class="container"([^>]*)>/i', $obj->data)) {
                //$pos = strrpos($obj->data, '</div>');
                //$str = base64_decode($this->_bfooter);
                //$obj->data = substr($obj->data, 0, $pos) . $str . substr($obj->data, $pos);
            } else {
                //$obj->data = str_replace('</body>', base64_decode($this->_bfooter) . '</body>', $obj->data);
            }
        }

        if('mobile' == Zend_Controller_Front::getInstance()->getRequest()->getModuleName() && !Zend_Controller_Front::getInstance()->getRequest()->isXmlHttpRequest()) {
            //$pos = strrpos($obj->data, '</div>');
            //$pos = strrpos($obj->data, '</div>', $pos - strlen($obj->data) - 1);
            //$str = base64_decode($this->_mfooter);
            //$obj->data = substr($obj->data, 0, $pos) . $str . substr($obj->data, $pos);
        }
    }
```

## Zmiany ustawień cache

/overloader.php

```php
$cacheLevel = Cache_Manager::CACHE_LEVEL_ALL_OBJECTS;
$cacheLifetime = 15552000;
```

## To do

* zmiana rozmiarów obrazków na kwadrat
* włączenie trybu debugowania
* rozmiary obrazków
* dodanie H1 i tekstu do producentów


