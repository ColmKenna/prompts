---
mode: agent
---
## Goal
Update the Android project branding based on a provided company name (`Clonmel`, `Sona`, `RB05`, or `Sample`). Treat the input as case-insensitive.

1. Ask (or read) which company should be applied.
2. Validate that the company is one of the supported values. If not, stop and ask for a valid name.
3. Use the mapping below to update all affected files.

| Company  | `SINGLE_SUPPLIER_LOCATION_CODE` | `TY_LOCATION_CODE` | `applicationId` suffix | `bannerImageView` drawable | `android:icon` drawable |
|----------|---------------------------------|--------------------|------------------------|----------------------------|-------------------------|
| Clonmel  | `CL09`                           | `CL09`             | `arcosretailerCL09`    | `@drawable/clonmellogo`    | `@drawable/strata_tri`  |
| Sona     | `SONA`                           | `SONA`             | `arcosretailerSONA`    | `@drawable/sonabanner`     | `@drawable/strata_tri`  |
| RB05     | `RB05`                           | `RB05`             | `arcosretailerRB05`    | `@drawable/rblogo2021`     | `@drawable/rblogo2021`  |
| Sample   | `AD00`                           | `AD00`             | `CL09` *               | `@drawable/strata_tri`     | `@drawable/strata_tri`  |

> *Sample uses `com.stratait.CL09` exactly (note it is not `arcosretailerCL09`).

## Files to update

### `Constants.java`
```java
public static final String SINGLE_SUPPLIER_LOCATION_CODE = "<value from table>";
public static final String TY_LOCATION_CODE = "<value from table>";
```

### `app/build.gradle`
```gradle
applicationId 'com.stratait.<suffix from table>'
```

### `activity_user_sign_in_view.xml`
```xml
android:id="@+id/bannerImageView"
app:srcCompat="<drawable from table>"
```

### `AndroidManifest.xml`
```xml
<application
    ...
    android:icon="<drawable from table>" />
```

Double-check that only the company-specific values are changed and no other configuration drifts.
