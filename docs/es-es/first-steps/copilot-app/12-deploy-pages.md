---
title: "Lección 12 - Publicar el sitio con GitHub Pages"
description: "Configura un flujo de trabajo de Pages con la aplicación GitHub Copilot, revisa su solicitud de incorporación de cambios y comprueba el Space Quiz publicado."
authors:
  - jamesmontemagno
lastUpdated: 2026-10-12
---

Publica el Space Quiz en GitHub Pages para que todo el mundo pueda acceder a él. Copilot puede preparar el flujo de trabajo y guiar el despliegue, pero debes revisar los cambios y confirmar que el sitio está disponible.

En esta lección, vas a:

- pedir a Copilot que configure el despliegue en GitHub Pages.
- revisar el flujo de trabajo y la solicitud de incorporación de cambios antes de combinarlos.
- seguir el despliegue y comprobar el sitio publicado.

## Pedir a Copilot que prepare el despliegue

Pide a Copilot que prepare un flujo de trabajo de GitHub Pages y una solicitud de incorporación de cambios, sin combinar los cambios ni publicar el sitio todavía.

> [!WARNING]
> Crear una solicitud de incorporación de cambios puede confirmar y enviar código. Revisa la configuración de aprobaciones y el repositorio de destino antes de enviar este prompt. Si está seleccionada la opción **Approve all**, no esperes una confirmación adicional.

En la sesión de la aplicación Copilot para `space-quiz`, envía el prompt siguiente:

1. Envía este prompt:

   ```plaintext
   Prepare a GitHub Pages deployment for this repository. Create a GitHub Actions workflow that builds the website if needed and deploys it to GitHub Pages after changes reach the default branch. Add a README describing the app and how to run it locally if one does not already exist. Create a pull request with these changes and report its link and any required Pages settings or permissions. Do not merge, enable auto-merge, change Pages settings, or deploy. Stop and wait for my review.
   ```

## Revisar el flujo de trabajo y la solicitud de incorporación de cambios

Revisa los cambios propuestos para confirmar que el flujo de trabajo solo publicará el sitio después de que los cambios lleguen a la rama predeterminada.

1. Confirma que el archivo README solo se ha añadido si el repositorio aún no tenía uno.
2. Revisa los cambios de la solicitud de incorporación, incluidos los pasos de compilación y despliegue del flujo de trabajo, sus activadores y permisos.
3. Confirma que el flujo de trabajo publica desde la rama predeterminada y no desde una solicitud de incorporación de cambios sin combinar.
4. Pide las correcciones necesarias y revisa el diff actualizado. Espera a que se superen las comprobaciones obligatorias de la solicitud antes de autorizar su combinación.

## Autorizar la combinación y el despliegue

Autoriza la combinación y la publicación únicamente después de revisar los cambios y confirmar el repositorio y la solicitud de incorporación de cambios.

> [!WARNING]
> El siguiente prompt autoriza al agente a combinar la solicitud revisada, cambiar la configuración de Pages del repositorio y publicar el sitio. Envíalo solo después de completar la revisión y confirmar el repositorio y la solicitud. La configuración de aprobaciones podría permitir estas acciones sin pedir otra confirmación.

Sustituye `<PR_NUMBER>` por el número de la solicitud que has revisado y envía el prompt siguiente por separado.

1. Envía este prompt:

   ```plaintext
   I have reviewed pull request <PR_NUMBER> in this repository and authorize its merge and deployment. Enable GitHub Pages using GitHub Actions, merge that pull request only after required checks pass, and monitor the deployment until it succeeds. Report the final URL of the live website. If you lack permission to enable Pages or merge, tell me exactly what I need to approve or do; do not report success until the site is live.
   ```

![Diagrama en tres etapas: una solicitud de incorporación de space-quiz combinada; una ejecución correcta de GitHub Actions Deploy website con los pasos Build website, Upload artifact y Deploy to Pages; y el Space Quiz publicado en yourname.github.io/space-quiz/.](../../../_images/first-steps-app-pages-deploy.svg)

## Comprobar el despliegue

Comprueba que la ejecución de GitHub Actions finaliza correctamente y que el sitio publicado se carga en la URL indicada.

1. Sigue la ejecución de despliegue de GitHub Actions hasta que termine correctamente.
2. Abre la URL indicada y confirma que se carga el Space Quiz.
3. Si el agente no puede habilitar Pages o combinar la solicitud, sigue las instrucciones específicas que te proporcione. No des la tarea por terminada hasta que el despliegue se complete y el sitio publicado se cargue.

## Resumen y siguientes pasos

Has:

- pedido a Copilot que configure el despliegue en GitHub Pages.
- revisado el flujo de trabajo y la solicitud antes de combinarlos.
- seguido el despliegue y comprobado el sitio publicado.

El Space Quiz ya está disponible y has verificado el despliegue en lugar de confiar únicamente en el informe del agente.

Continúa con [la lección 13: Repaso y próximos pasos][next-lesson].

[next-lesson]: ../13-review/
