# Bolivia MAE website

We build https://boliviamaes.github.io/site/ with Jekyll (GitHub Pages) and Observable embeds.

```js
gabinete = md`## Gabinete del Estado Plurinacional de Bolivia

### Presidente y Vicepresidente

${getEntityAuthorityCard(getEntityById(presidenciaId))}
${getEntityAuthorityCard(getEntityById(vicepresidenciaId))}

### Ministros

${getMinistrosCards().join("\n")}
`
```

```js echo
gabinete2 = html`
<div data-scoped-0 class="bg-gray-100 text-gray-900 font-sans antialiased p-16" >

<h2>Gabinete del Estado Plurinacional de Bolivia</h2>

<h3>Presidente y Vicepresidente</h3>

${getEntityAuthorityCard(getEntityById(presidenciaId))}
${getEntityAuthorityCard(getEntityById(vicepresidenciaId))}

<h3>Ministros</h3>

${getMinistrosCards().join("\n")}
</div>
`
```

```js
main3 = html`


<div data-scoped-0 class="bg-gray-100 text-gray-900 font-sans antialiased p-16">
<div class="bg-white shadow overflow-hidden sm:rounded-lg p-8">
<table class="table-auto">
  <thead>
    <tr>
      <th class="w-1/2 px-4 py-2">Title</th>
      <th class="w-1/4 px-4 py-2">Author</th>
      <th class="w-1/4 px-4 py-2">Views</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="border px-4 py-2">Intro to CSS</td>
      <td class="border px-4 py-2">Adam</td>
      <td class="border px-4 py-2">858</td>
    </tr>
    <tr class="bg-gray-100">
      <td class="border px-4 py-2">A Long and Winding Tour of the History of UI Frameworks and Tools and the Impact on Design</td>
      <td class="border px-4 py-2">Adam</td>
      <td class="border px-4 py-2">112</td>
    </tr>
    <tr>
      <td class="border px-4 py-2">Intro to JavaScript</td>
      <td class="border px-4 py-2">Chris</td>
      <td class="border px-4 py-2">1,280</td>
    </tr>
  </tbody>
</table>
</div>
</div>
`
```

---

  ## Internals

---

### Code

```js
getPersonNameFromEntityId = (entityId) => {
  const entity = getEntityById(entityId);
  return getAuthorityPerson(getCurrentAuthority(entity)).nombre;
}
```

```js
getEntityById = (entityId) => {
  const entity = data.entidades.find((d) => d.id === entityId);
  if (!entity) {
    throw new Error("Cannot find the entity for this id");
  }
  return entity;
}
```

```js
getCurrentAuthority = (entity) => {
  const list = data.autoridades.filter(
    (d) => !d.hasta && entity.autoridades.includes(d.airtableId)
  );
  if (list.length !== 1) {
    throw new Error("Cannot find the current authority");
  }
  return list[0];
}
```

```js
getAuthorityPerson = (authority) => {
  if (authority.persona.length !== 1) {
    throw new Error("Authority 'persona' field should contain one person");
  }
  const personId = authority.persona[0];
  const person = data.personas.find((d) => d.airtableId === personId);
  if (!person) {
    throw new Error("Cannot find the authority person");
  }
  return person;
}
```

```js
getEntityAuthorityCard = (entity) => {
  const authority = getCurrentAuthority(entity);

  return `
<!-- Profile Card -->
<div class="rounded-lg shadow-lg bg-gray-600 w-full  h-24 flex flex-row flex-wrap p-3 antialiased">
  <div class="w-16 h-16">
    <img class="rounded-lg shadow-lg antialiased" src="https://cdn.pixabay.com/photo/2015/10/05/22/37/blank-profile-picture-973460_960_720.png">  
  </div>
  <div class="md:w-2/3 w-full px-3 flex flex-row flex-wrap">
    <div class="w-full text-right text-gray-700 font-semibold relative pt-3 md:pt-0">
      <div class="text-2xl text-white leading-tight">${
        getAuthorityPerson(authority).nombre
      }</div>
      <div class="text-normal text-gray-300 hover:text-gray-400 cursor-pointer"><span class="border-b border-dashed border-gray-500 pb-1">${
        entity.nombre
      }</span></div>
      <div class="text-sm text-gray-300 hover:text-gray-400 cursor-pointer md:absolute pt-3 md:pt-0 bottom-0 right-0">desde el : <b>${getAuthorityStart(
        authority
      )}</b></div>
    </div>
  </div>
</div>
<!-- End Profile Card -->


</article>
`;
}
```

```js
getMinistrosCards = () => {
  const ministerios = data.entidades
    .filter((d) => d.tipo === "Ministerio")
    .sort((a, b) => d3.ascending(a.nombre, b.nombre));
  return ministerios.map((d) => getEntityAuthorityCard(d));
}
```

```js
getAuthorityStart = (authority) =>
  isoformat.parse(authority.desde).toLocaleDateString("es-BO", {
    weekday: "long",
    year: "numeric",
    month: "long",
    day: "numeric"
  })
```

```js
isoformat = import("isoformat")
```

```js
(reset, tailwindStyles)
```

```js
reset = html`<style>table { width: unset; max-width: unset; font-family: unset; font-size: unset; border-collapse: unset; } th { text-align: unset; }</style>`
```

```js
import { tailwindStyles } from "@hastebrot/hello-tailwind-css"
```

### Data

```js
url = "https://raw.githubusercontent.com/BoliviaMaes/bolivia-maes/main/bolivia-maes.json"
```

```js
data = d3.json(url, { autotyped: true })
```

```js
vicepresidenciaId = 70
```

```js
presidenciaId = 65
```

```js
viewof tableName = Inputs.radio(Object.keys(data), {
  value: Object.keys(data)[0]
})
```

```js
viewof search = Inputs.search(data[tableName])
```

```js
viewof entry = Inputs.table(search)
```
