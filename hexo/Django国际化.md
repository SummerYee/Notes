---
title: Django国际化
categories: 
- WEB
tags:
- WEB后端
---
Django国际化
Django 支持国际化，多语言。Django的国际化是默认开启的，如果您不需要国际化支持，那么您可以在您的设置文件中设置 USE_I18N = False，那么Django会进行一些优化，不加载国际化支持机制。

NOTE: 18表示Internationalization这个单词首字母I和结尾字母N之间的字母有18个。I18N就是Internationalization（国际化）的意思。

#一，开启国际化的支持，需要在settings.py文件中设置
# 翻译文件所在目录，需要手工创建
# settings
LOCALE_PATHS = (
    os.path.join(BASE_DIR, 'locale'),
)

# 二，生成需要翻译的文件,初次创建
python manage.py makemessages -l zh_hans
python manage.py makemessages -l en
# 后面更新
python manage.py makemessages

# 三，手工翻译 locale 中的 django.po
msgstr ""

# 四，编译一下，这样翻译才会生效
python manage.py compilemessages

前端
`{% load i18n %}`