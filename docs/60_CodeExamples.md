# Code Examples

## I18n Context
| Name | Description |
|------|-------------|
| getRouteItem | return `RouteItemInterface` |
| getZone | returns `ZoneInterface` |
| getLocaleDefinition | returns `LocaleDefinitionInterface` |
| getCurrentZoneSite | returns `ZoneSiteInterface` |
| getCurrentLocale | get current locale |
| getCurrentLocaleInfo | get detailed information about active locale |
| getLocaleInfo | get locale info of given locale from locale  |
| getZoneDefaultLocale | get default locale of zone |
| getZoneActiveLocales | returns an array of all active/allowed locales in zone |
| getZoneGlobalInfo | international state |
| getCurrentCountryAndLanguage | get country and locale info |
| getLinkedLanguages | get all (raw) linked documents of current route |
| getActiveLanguages | helper method to list all active languages of current route (see [navigation code example](./60_CodeExamples.md#navigation-examples)) |
| getActiveCountries | helper method to list all active countries and languages of current route (see [navigation code example](./60_CodeExamples.md#navigation-examples)) |
| getLanguageNameByIsoCode | helper to get language name by iso code |
| getCountryNameByIsoCode | helper to get country name by iso code |

### Fetch Context Data in Twig
```twig
{# 
    be careful, i18n_current_context is allowed to return null!
#}

{% set current_locale = i18n_current_context().localeDefinition.locale %}
{% set current_language_iso = i18n_current_context().localeDefinition.languageIso %}
{% set current_country_iso = i18n_current_context().localeDefinition.countryIso %}

{{ dump(i18n_current_context().localeDefinition.locale) }}
{{ dump(i18n_current_context().linkedLanguages) }}
{{ dump(i18n_current_context().activeLanguages) }}
{{ dump(i18n_current_context().activeCountries) }}
{{ dump(i18n_current_context().languageNameByIsoCode(current_language_iso, current_locale)) }}
{{ dump(i18n_current_context().countryNameByIsoCode(current_country_iso, current_locale)) }}

{{ dump(i18n_current_context().zoneActiveLocales()) }}
{{ dump(i18n_current_context().localeInfo('de', 'id')) }}
```

### Fetch Context Data in PHP
```php
<?php

use Symfony\Component\HttpFoundation\RequestStack;
use I18nBundle\Http\I18nContextResolverInterface;

class Service
{
    public function myAction(RequestStack $requestStack, I18nContextResolverInterface $i18nContextResolver)
    {
        $i18nContext = $i18nContextResolver->getContext($requestStack->getMainRequest());
        
        $LinkedLanguages = $i18nContext->getLinkedLanguages();
        
        // get current locale
        $currentLocale = $i18nContext->getLocaleDefinition()->getLocale();

        // get current language iso
        $currentLanguageIso = $i18nContext->getLocaleDefinition()->getLanguageIso();

        // get linked languages
        $linkedLanguages = $i18nContext->getLinkedLanguages();

        // get linked languages, but only rootDocuments
        $linkedLanguages = $i18nContext->getLinkedLanguages(true);

        // get language name by iso code
        $languageName = $i18nContext->getLanguageNameByIsoCode($currentLanguageIso, $currentLocale);
        
        // locale provider info
        $localProviderActiveLocales = $i18nContext->getZoneActiveLocales();
        $localeProviderLocaleInfo = $i18nContext->getLocaleInfo('de', 'id');
    }
}
```

## Zone
The zone represents an instance of `I18nZoneInterface` which comes with some helper methods:

| Name | Description |
|------|-------------|
| getId | given zone id (null, if it no zones have been configured |
| getName | given zone name (null, if no zones have been configured |
| getDomains | all available domains for given zone. |
| getMode | returns `language` or `country` |
| getTranslations | array, translations for dynamic routes |
| isActiveZone | check if zone is active one |
| getLocaleUrlMapping | For example: `de-ch`. Mostly used to build [static routes](./28_StaticRoutes.md#naming-convention-in-country-context) |
| getGlobalInfo | international state  |
| getSites | get all corresponding sites  |


### Fetch Zone Data in Twig
```twig
{# 
    be careful, i18n_current_context is allowed to return null!
#}

{{ dump(i18n_current_context().zone.mode) }}
{{ dump(i18n_current_context().zone.sites) }}

```

### Fetch Zone Data in PHP
```php
<?php

use Symfony\Component\HttpFoundation\RequestStack;
use I18nBundle\Http\I18nContextResolverInterface;

class Service
{
    public function myAction(RequestStack $requestStack, I18nContextResolverInterface $i18nContextResolver)
    {
        $i18nContext = $i18nContextResolver->getContext($requestStack->getMainRequest());
        
        $zone = $i18nContext->getZone();
        
        // get zone mode (language or country
        $mode = $zone->getMode();
        
        // list all zone sites
        $zoneSites = $zone->getSites(true);
    }
}
```

### Zone Current Site Information
To retrieve data from an active site, you may want to use the `$i18nContext->getCurrentZoneSite()` method. 
Since the current site gets defined via the current locale, be sure that locale is always available.

The current site represents an instance of `ZoneSiteInterface` which comes with some helper methods:

| Name | Description |
|------|-------------|
| getSiteRequestContext | returns `SiteRequestContext` object
| SiteRequestContext::getScheme | For example: `http` |
| SiteRequestContext::getHttpPort | For example: 80 |
| SiteRequestContext::getHttpsPort | For example: 443 |
| SiteRequestContext::getDomainUrl | For example: `https://pimcore5-domain4.test` |
| SiteRequestContext::getHost | For example: `www.pimcore5-domain4.test` |
| SiteRequestContext::getNone3wHost | For example: `pimcore5-domain4.test` |
| isRootDomain | bool |
| getLocale | For example: `de_CH` |
| getCountryIso | For example: `CH` |
| getLanguageIso | For example: `de` |
| getHrefLang | For example: `de-ch` |
| getLocaleUrlMapping | For example: `de-ch`. Mostly used to build [static routes](./28_StaticRoutes.md#naming-convention-in-country-context). |
| getUrl | For example: `https://pimcore5-domain4.test/de-ch` |
| getHomeUrl | string |
| getFullPath | For example: `domain4/de-ch` |
| getType | For example: `hardlink` |
| getSubSites | array |
| hasSubSites | bool |

### Fetch Zone Site Data in Twig
```twig
{# get current context info #}
{{ dump(i18n_current_context().currentZoneSite.url) }}
{{ dump(i18n_current_context().currentZoneSite.localeUrlMapping) }}
```

### Fetch Zone Site Data in Twig
```php
<?php

use Symfony\Component\HttpFoundation\RequestStack;
use I18nBundle\Http\I18nContextResolverInterface;

class Service
{
    public function myAction(RequestStack $requestStack, I18nContextResolverInterface $i18nContextResolver)
    {
        $i18nContext = $i18nContextResolver->getContext($requestStack->getMainRequest());
        
        $currentContextInfo = $i18nContext->getCurrentZoneSite()->getUrl();
        $currentContextInfo = $i18nContext->getCurrentZoneSite()->getLocaleUrlMapping();
    }
}
```

## Navigation Examples

### Language Drop-Down
```twig
{% set current_context = i18n_current_context() %}
{% if current_context is not null %}
    {% set languages = current_context.activeLanguages %}
    {% if languages is iterable %}
        <nav id="navigation">
            <select>
                {% for language in languages %}
                    <option {{ language.active ? 'selected' : '' }} value="{{ language.linkedHref }}">{{ language.iso|upper }}</option>
                {% endfor %}
            </select>
        </nav>
    {% endif %}
{% endif %}
```

### Country Selection
```twig
{% set current_context = i18n_current_context() %}
{% if current_context is not null %}
    {% set countries = current_context.activeCountries %}
    {% if countries is iterable %}
        <nav id="navigation">
            {% for country in countries %}
                <ul>
                    <li class="country">{{ country.countryTitle }}
                        <ul class="languages">
                            {% for language in country.languages %}
                                <li{{ language.active ? ' class="active"' : '' }}><a href="{{ language.linkedHref }}">{{ language.iso|upper }}</a></li>
                            {% endfor %}
                        </ul>
                    </li>
                </ul>
            {% endfor %}
        </nav>
    {% endif %}
{% endif %}
```

### Complex Country / Language Selection based on Current Zone
```twig
<nav id="navigation">
    {% set current_context = i18n_current_context() %}
    {% if current_context is not null %}
        {% if current_context.zone.mode == 'country' %}
            {% set countries = current_context.activeCountries %}
            {% if countries is iterable %}
                {% for country in countries %}
                    <ul>
                        <li class="country">{{ country.countryTitle }}
                            <ul class="languages">
                                {% for language in country.languages %}
                                    <li{{ language.active ? ' class="active"' : '' }}><a href="{{ language.linkedHref }}">{{ language.iso|upper }}</a></li>
                                {% endfor %}
                            </ul>
                        </li>
                    </ul>
                {% endfor %}
            {% endif %}
        {% elseif current_context.zone.mode == 'language' %}
            {% set languages = current_context.activeLanguages %}
            {% if languages is iterable %}
                <select>
                    {% for language in languages %}
                        <option {{ language.active ? 'selected' : '' }} value="{{ language.linkedHref }}">{{ language.iso|upper }}</option>
                    {% endfor %}
                </select>
            {% endif %}
        {% endif %}
    {% endif %}
</nav>
```