---
title: Crear un sitio de Páginas de GitHub
intro: 'Puede crear un sitio de {% data variables.product.prodname_pages %} en un repositorio nuevo o existente.'
redirect_from:
  - /articles/creating-pages-manually
  - /articles/creating-project-pages-manually
  - /articles/creating-project-pages-from-the-command-line
  - /articles/creating-project-pages-using-the-command-line
  - /articles/creating-a-github-pages-site
  - /github/working-with-github-pages/creating-a-github-pages-site
product: '{% data reusables.gated-features.pages %}'
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
  ghec: '*'
topics:
  - Pages
shortTitle: Crear un sitio de GitHub Pages
---

{% data reusables.pages.org-owners-can-restrict-pages-creation %}

## Crear un repositorio para tu sitio

{% data reusables.pages.new-or-existing-repo %}

{% data reusables.repositories.create_new %}
{% data reusables.repositories.owner-drop-down %}
{% indented_data_reference reusables.pages.emu-org-only spaces=3 %}
{% data reusables.pages.create-repo-name %}
{% data reusables.repositories.choose-repo-visibility %}
{% data reusables.repositories.initialize-with-readme %}
{% data reusables.repositories.create-repo %}

## Crear tu sitio

{% data reusables.pages.must-have-repo-first %}

{% data reusables.pages.private_pages_are_public_warning %}

{% data reusables.pages.navigate-site-repo %}
{% data reusables.pages.decide-publishing-source %}
1. Crea el archivo de entrada para tu sitio. {% data variables.product.prodname_pages %} buscará un archivo `index.html`, `index.md` o `README.md` como el archivo de entrada para tu sitio.

   {% ifversion pages-custom-workflow %}Si tu fuente de publicación es una rama y carpeta, el archivo de entrada debe estar en el nivel superior de la carpeta origen en la rama origen. Por ejemplo, si tu fuente de publicación es la carpeta `/docs` en la rama `main`, tu archivo de entrada debe estar ubicado en la carpeta `/docs` en una rama llamada `main`.

   Si tu fuente de publicación es un flujo de trabajo de {% data variables.product.prodname_actions %}, el artefacto que despliegues deberá incluir el archivo de entrada en el nivel superior del mismo. En vez de agregar el archivo de entrada a tu repositorio, puedes elegir que tu flujo de trabajo de {% data variables.product.prodname_actions %} genere tu archivo de entrada cuando se ejecute.{% else %} El archivo de entrada debe estar en el nivel superior de la fuente de publicación que elijas. Por ejemplo, si tu fuente de publicación es la carpeta `/docs` en la rama `main`, tu archivo de entrada debe estar ubicado en la carpeta `/docs` en una rama llamada `main`.{% endif %}
{% data reusables.pages.configure-publishing-source %}
{% data reusables.repositories.sidebar-settings %}
{% data reusables.pages.sidebar-pages %}
{% data reusables.pages.choose-visibility %}
{% data reusables.pages.visit-site %}
{% data reusables.pages.check-workflow-run %}

{% data reusables.pages.admin-must-push %}

## Pasos siguientes

Puedes agregar más páginas a tu sitio creando más archivos nuevos. Cada archivo estará disponible en tu sitio en la misma estructura de directorios que tu fuente de publicación. Por ejemplo, si la fuente de publicación para tu sitio de proyectos es la rama `gh-pages`, y creas un archivo nuevo denominado `/about/contact-us.md` en la rama `gh-pages`, el archivo estará disponible en {% ifversion fpt or ghec %}`https://<user>.github.io/<repository>/{% else %}`http(s)://<hostname>/pages/<username>/<repository>/{% endif %}about/contact-us.html`.

También puedes agregar un tema para personalizar la apariencia de tu sitio. Para obtener más información, consulta la sección "[Agregar un tema a tu sitio de {% data variables.product.prodname_pages %} utilizando Jekyll](/articles/adding-a-theme-to-your-github-pages-site-using-jekyll)".

Para personalizar aún más tu sitio, puedes usar Jekyll, un generador de sitio estático con soporte integrado para {% data variables.product.prodname_pages %}. Para obtener más información, consulta la sección "[Acerca de {% data variables.product.prodname_pages %} y Jekyll](/articles/about-github-pages-and-jekyll)".

## Leer más

- "[Solucionar problemas de errores de construcción de Jekyll para sitios de {% data variables.product.prodname_pages %}](/articles/troubleshooting-jekyll-build-errors-for-github-pages-sites)"
- "[Crear y eliminar ramas dentro de tu repositorio](/articles/creating-and-deleting-branches-within-your-repository/)"
- "[Crear archivos nuevos](/articles/creating-new-files)"
