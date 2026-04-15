# Using Feature Flags

Feature flags are a way to control feature availability in an application without changing code at runtime. They help teams safely release features, perform incremental rollouts, and manage experimental functionality.

## High-Level Overview

- Identify the feature to be controlled using a feature flag  
- Locate or define the feature configuration in the deployment config  
- Assign a unique feature identifier for the feature  
- Enable or disable the feature using configuration flags  
- Use feature flags in code to conditionally render UI or execute logic

## Step-by-Step Guide

1. Identify the High-Level Feature

First, determine which feature you want to control using a feature flag. For example, let's say we want to control the visibility
of a new "filter by metadata attribute" input field in organizations page. In this case, the high level feature is "organizations".

2. Locate Feature Config in Deployment Config

The deployment configuration in each React app includes a section for feature flags. Go to the file and locate the relevant
feature config object for your feature.

deployment.config.json:

```js
{
  "ui": {
    "features": [
      // other features,
      "organizations": { 
        "disabledFeatures": [],
        "enabled": true,
        "scopes": {
          "create": [ 
            "internal_organization_create"
          ],
          "delete": [ "internal_organization_delete" ],
          "feature": [ "console:organizations" ],
          "read": [ "internal_organization_view" ], 
          "update": [ "internal_organization_update" ] 
        }
      },   
    ]
  }
}
```

3. Choose a Suitable Feature Identifier

Select a unique and descriptive identifier for your feature. For our example, we'll use `organizations.filterByMetadataAttributes`.

4. Configure Feature State in Deployment Config

Feature flags are managed using the `disabledFeatures` array or equivalent feature configuration.

Add your feature identifier to control its availability.

Updated deployment.config.json:

```js
{
  "ui": {
    "features": [
      // other features,
      "organizations": { 
        "disabledFeatures": [
          "organizations.filterByMetadataAttributes"
        ],
        "enabled": true,
        "scopes": {
          "create": [ 
            "internal_organization_create"
          ],
          "delete": [ "internal_organization_delete" ],
          "feature": [ "console:organizations" ],
          "read": [ "internal_organization_view" ], 
          "update": [ "internal_organization_update" ] 
        }
      },   
    ]
  }
}
```

5. Use Feature Flags in Your Code

Finally, use the feature flag to conditionally show/hide UI elements or run specific logic in your React components.

Example:

```js
import { isFeatureEnabled } from "@wso2is/core/helpers"

const App = () => {
  const organizationFeatureConfig: string[] = useSelector((state: AppState) =>
        state.config.ui.features?.organizations);

  return (
    <div>
      <h1>Organizations</h1>
      {isFeatureEnabled(organizationFeatureConfig, "organizations.filterByMetadataAttributes") && (
        <Select>
          <label>Select metadata attribute</label>
        </Select>
      )}
    </div>
  );
};

export default App;
```

In the above example, the "filter by metadata attribute" input is conditionally rendered based on the defined feature flag. If
`organizations.filterByMetadataAttributes` is included in the `disabledFeatures` array, the section will not be displayed.

## Best Practices

- Always use clear and descriptive feature identifiers  
- Remove unused feature flags once a feature is stable  
- Avoid overusing feature flags to reduce code complexity  

## Conclusion

Feature flags provide a safe and scalable way to manage feature rollouts in identity-apps. They help teams control feature visibility, reduce deployment risks, and support incremental development.
