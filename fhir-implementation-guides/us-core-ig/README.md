---
description: Learn how to enable US Core
---

# 🎓 US Core IG

In this tutorial, we'll explain how to configure Aidbox to use [US Core Implementation Guide](https://www.hl7.org/fhir/us/core/) (US Core IG).

## Support

US Core IG contains the following FHIR artifacts: Profiles, Extensions, Terminology, Search Parameters, Operations and Capability Statements.

{% content-ref url="us-core-ig-support-reference.md" %}
[us-core-ig-support-reference.md](us-core-ig-support-reference.md)
{% endcontent-ref %}

## Steps

{% hint style="warning" %}
Currently it is only possible to use FHIR IGs for on-premise installations. We are working to add support for other distributions as well.
{% endhint %}

First of all, set up Aidbox instance. You can follow this [guide](../../getting-started/run-aidbox-locally-with-docker/).

After Aidbox is ready to use, you need to specify US Core dependency.

Put the following in `zen-package.edn`

```
{:deps {us-core "https://"}}
```

You can read more about providing IGs here [link!](../../aidbox-configuration/aidbox-zen-lang-project/enable-igs.md)&#x20;

If you put US Core IG dependency while Aidbox is running you need to restart it so that it pull the dependency and consumes it.