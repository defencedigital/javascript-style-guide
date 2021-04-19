# Application structure
This application structure guide is in `[DRAFT]` and is open for discussion and change.

It should be used as a guide on how to structure generic JavaScript applications in any framework.

## Folder structure
The folder structure should clearly segregate concerns of the applications, providing layers within the application

```
package.json
/node_modules
/src
  /controllers
    /MyController
      /MyController.test.ts
      /MyController.ts
  /components
    /MyComponent
      /MyComponent.test.tsx
      /MyComponent.tsx
      /my-component.scss
  /pages
    /MyPage
      /MyPage.test.tsx
      /MyPage.tsx
      /my-page.scss
  /services
    /MyService
      /MyService.ts
      /MyService.test.ts
  /utils
    /myUtil
      /myUtil.ts
      /myUtil.test.ts
```
_Why_: Allows developers to instinctively find code of concern

## Controllers orchestrate, services instrument
_Why_: follows the MVCS pattern closely and allows clean, readable abstraction of tasks, allows segregation of error handling to the relvant area of code and promotes self documentation.

```js
// Bad
MyController.ts
async () => {
  try {
    const data = await fetch('https://maps.os.com/some-api/');

    return data.map((poi) => {
      const wgs84 = new GT_WGS84();
      wgs84.setDegrees(poit.lon, poi.lat);
      try {
        const osgb = wgs84.getOSGB();
        return osgb.getGridRef(3);
      } catch(e) {
        // handle conversion error
      }
    })
  } catch(e) {
    // handle fetch error
  }
}


// Good
MyController.ts
import DataService from '../../services/DataService';
import ConversionSvc from '../../services/ConversionService';

async () => {
  const data = await DataService.getData();
  return ConversionSvc.toWGS84(data);
}
```

## Services
### Should be singleton classes

_Why_: Allows commonality across the application.

```js
// bad
MyService.ts
export default class MyService {
  ...
}

// good
MyService.ts

class MyService {
  ...
}

export default Object.freeze(new MyService());
```