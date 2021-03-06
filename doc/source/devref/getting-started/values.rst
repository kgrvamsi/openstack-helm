Default Values
--------------

Two major philosophies guide the OpenStack-Helm values approach. It is
important that new chart developers understand the ``values.yaml``
approach OpenStack-Helm has within each of its charts to ensure that all
charts are both consistent and remain a joy to work with.

The first philosophy to understand is that all charts should be
independently installable and should not require a parent chart. This
means that the values file in each chart should be self-contained. The
project avoids using Helm globals and parent charts as requirements for
capturing and feeding environment specific overrides into subcharts. An
example of a single site definition YAML that can be source controlled
and used as ``--values`` input to all OpenStack-Helm charts to maintain
overrides in one testable place is forthcoming. Currently Helm does not
support a ``--values=environment.yaml`` chunking up a larger override
file's YAML namespace. Ideally, the project seeks native Helm support
for ``helm install local/keystone --values=environment.yaml:keystone``
where ``environment.yaml`` is the operator's chart-wide environment
definition and ``keystone`` is the section in environment.yaml that will
be fed to the keystone chart during install as overrides. Standard YAML
anchors can be used to duplicate common elements like the ``endpoints``
sections. At the time of writing, operators can use a temporary approach
like
`values.py <https://github.com/att-comdev/openstack-helm/blob/master/helm-toolkit/utils/values/values.py>`__
to chunk up a single override YAML file as input to various individual
charts. Overrides, just like the templates themselves, should be source
controlled and tested, especially for operators operating charts at
scale. This project will continue to examine efforts such as
`helm-value-store <https://github.com/skuid/helm-value-store>`__ and
solutions in the vein of
`helmfile <https://github.com/roboll/helmfile>`__. Another compelling
project that seems to address the needs of orchestrating multiple charts
and managing site specific overrides is
`Landscape <https://github.com/Eneco/landscaper>`__

The second philosophy is that the values files should be consistent
across all charts, including charts in core, infra, and add-ons. This
provides a consistent way for operators to override settings, such as
enabling developer mode, defining resource limitations, and customizing
the actual OpenStack configuration within chart templates without having
to guess how a particular chart developer has laid out their
values.yaml. There are also various macros in the ``helm-toolkit`` chart
that will depend on the ``values.yaml`` within all charts being
structured a certain way.

Finally, where charts reference connectivity information for other
services sane defaults should be provided. In cases where these services
are provided by OpenStack-Helm itself, the defaults should assume that
the user will use the OpenStack-Helm charts for those services, but
should also allow those charts to be overridden if the operator has them
externally deployed.
