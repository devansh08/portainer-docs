# Updating the Edge Agent

To update the Portainer Edge Agent to the latest version, follow the below instructions for your Edge environment.

{% hint style="info" %}
Always match the agent version to the Portainer Server version. In other words, when you're installing or updating to Portainer 2.21.0 make sure all of the agents are also on version 2.21.0.
{% endhint %}

{% hint style="danger" %}
Before beginning any update, we highly recommend [taking a backup](../../administering-portainer/settings/#backup-portainer) of your current Portainer configuration.
{% endhint %}

## Docker Standalone

{% hint style="info" %}
Portainer now also has the ability to update Edge Agents on Docker Standalone [directly from within the UI](../../administering-portainer/environments/update.md).
{% endhint %}

To upgrade the Portainer Edge Agent on a Docker Standalone platform, you will first need to note the **Edge identifier** and the **Edge key** for the Edge environment. To find these values, log into Portainer and click **Environments**, then click the name of the environment you are updating.

At the top of the page in the **Edge information** section, you will see the two values you require in the next steps.

<figure><img src="../../.gitbook/assets/2.15-upgrade-edge-edgeinfo.png" alt=""><figcaption></figcaption></figure>

Next, on the Edge environment, we need to stop and remove the Edge Agent container.

```
docker stop portainer_edge_agent
docker rm portainer_edge_agent
```

We also want to ensure we have the updated version of the container image locally:

```
docker pull portainer/agent:2.21.0
```

To deploy the updated Edge Agent, replace the `your-edge-identifier-here` and `your-edge-key-here` values in the following command with those you retrieved earlier, then run the command:

```
docker run -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes -v /:/host -v portainer_agent_data:/data --restart always -e EDGE=1 -e EDGE_ID=your-edge-identifier-here -e EDGE_KEY=your-edge-key-here -e EDGE_INSECURE_POLL=1 --name portainer_edge_agent portainer/agent:2.21.0
```

## Docker Swarm

To update the Portainer Edge Agent on a Docker Swarm environment, run the following commands.

First, to ensure you have the updated container image locally, pull the image:

```
docker pull portainer/agent:2.21.0
```

Then, update the service to use the new image version:

```
docker service update --image portainer/agent:2.21.0 --force portainer_edge_agent 
```

## Kubernetes

To update the Portainer Edge Agent on a Kubernetes environment, you will need to first download an updated YAML manifest, then apply that manifest to your existing environment.

To download the manifest, you can use one of the following commands:

{% tabs %}
{% tab title="Business Edition" %}
```
curl -L https://downloads.portainer.io/ee2-21/portainer-agent-edge-k8s.yaml  -o portainer-agent-edge-k8s.yaml
```
{% endtab %}

{% tab title="Community Edition" %}
```
curl -L https://downloads.portainer.io/ce2-21/portainer-agent-edge-k8s.yaml -o portainer-agent-edge-k8s.yaml  
```
{% endtab %}
{% endtabs %}

To apply this manifest to your environment, run the following command:

```
kubectl apply -f portainer-agent-edge-k8s.yaml
```
