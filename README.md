Nette Translator (c) Patrik Votoček (Vrtak-CZ), 2010 (http://patrik.votocek.cz)


Note
========
This is short manual how to use Nette Translator in the newest Nette 2.0 in its most simple version.
No need to edit or operate with .po/.mo files required. Written 2012-02-10.

Actual info/manual: http://wiki.nette.org/cs/cookbook/zprovozneni-prekladace-nettetranslator


### 1. Enable Translator

config.neon:

	common:
		services:
			translator:
				factory: NetteTranslator\Gettext::getTranslator
				setup:
					- addFile(%appDir%/lang, front) # at leas one file required
					- NetteTranslator\Panel::register # panel to debug bar

NOTE: **Do not forget to make folder __%appDir%/lang__ writable**

### 2. Use in templates

default.latte:

	{_"Dog"}
	{_"Cat", $number} // for plural, default are Czech plurals: 1, 2-4, 5+


### 3. Use in forms

```php
/**
 * Base presenter for all application presenters.
 */
abstract class BasePresenter extends Flame\Application\UI\Presenter
{

	/**
	 * @persistent
	 */
	public $lang;

	/**
	 * @autowire
	 * @var \NetteTranslator\Gettext
	 */
	protected $translator;

	/**
	 * @autowire
	 * @var \Nette\Http\Request
	 */
	protected $request;

	protected function startup()
	{
		parent::startup();

		if (!$this->lang) {
			$lang = $this->request->detectLanguage(array('en', 'cs')) ?: 'cs';
			$this->redirectUrl($lang);
		}

	}

	public function createTemplate($class = NULL)
	{
		$template = parent::createTemplate($class);
		$this->translator->setLang($this->lang); // set lang
		$template->setTranslator($this->translator);
		return $template;
	}

}
```php
