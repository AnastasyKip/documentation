# Start Aidbox with US Core IG enabled

## Steps

{% hint style="warning" %}
Currently it is only possible to use FHIR IGs for self-hosted installations. We are working to add support for other distributions.
{% endhint %}

You need to go through the following steps:

1. Set up base Aidbox instance
2. Specify US Core as a dependency
3. Commit changes to your Aidbox configuration project
4. Start Aidbox and verify that IGs are enabled

All of them are covered in a greater detail below.

{% hint style="info" %}
If you are new to Aidbox it is strongly recommended not to skip any steps. Otherwise you may end up with a misconfigured instance.
{% endhint %}

### Set up base Aidbox instance

Set up Aidbox instance by following our quick start guide.

{% content-ref url="../../../getting-started/run-aidbox-locally-with-docker/" %}
[run-aidbox-locally-with-docker](../../../getting-started/run-aidbox-locally-with-docker/)
{% endcontent-ref %}

### Specify US Core as a dependency

Put the following content in `zen-package.edn`:

```clojure
{:deps {hl7-fhir-us-core "https://github.com/zen-fhir/hl7-fhir-us-core.git"}}
```

Go to the main configuration file for your system (`system.edn` if you followed the quick start guide from above) and add `hl7-fhir-us-core` namespace to imports. It should look like this:

```clojure
{…
 :import #{aidbox hl7-fhir-us-core maybe-some-other-deps}
 …}
```

Commit the changes you just made:

```shell
git add . && git commit -m "Add us-core dependency"
```

Commit step is necessary as we are using [Aidbox configuration project](../../../aidbox-configuration/aidbox-zen-lang-project/) which is a git repository under the hood.

### Start Aidbox

Start Aidbox by running the following command:

```shell
docker compose up -d
```

It should be available now at `http://localhost:8888`.

### Verify that US Core IG works

Go to “Profiles” page in the Aidbox UI (`http://localhost:8888/ui/zen-ui`) and make sure that `hl7-fhir-us-core` is listed among the namespaces.

You can also verify that validation with US Core IG works. For that you can make the following HTTP requests and check that response statuses are returned as expected.

{% code title="Status: 201" %}
```
POST /Patient
content-type: text/yaml
accept: text/yaml

meta:
  profile:
    - "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
birthsex: "F"
gender: "female"
name:
  - {use: "anonymous"}
identifier:
  - {system: "some-system", value: "unique-value"}
```
{% endcode %}

This request checks that valid values for `birthsex` field are allowed.

{% code title="Status: 422" %}
```
POST /Patient
content-type: text/yaml
accept: text/yaml

meta:
  profile:
    - "http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient"
birthsex: "SOMETHING-UNKNOWN"
gender: "female"
name:
  - {use: "anonymous"}
identifier:
  - {system: "some-system", value: "unique-value"}
```
{% endcode %}

This request checks that invalid values for `birthsex` field are not allowed.

## Resources

If you want to learn more about working with IGs in Aidbox, there is a special page dedicated to it.

{% content-ref url="../../../aidbox-configuration/aidbox-zen-lang-project/enable-igs.md" %}
[enable-igs.md](../../../aidbox-configuration/aidbox-zen-lang-project/enable-igs.md)
{% endcontent-ref %}
