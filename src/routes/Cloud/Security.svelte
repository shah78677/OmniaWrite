<script>
  import { _ } from "svelte-i18n";

  import { settings } from "../../stores";
  import cloud from "../../appwrite";
  import moment from "moment";
  import "moment/locale/de";

  import Spinner from "../../shared/Spinner.svelte";
  import { Table, Cell, Row, Heading } from "../../components/Table";

  moment.locale($settings.language);

  const logoutSession = id => {
    cloud.logoutSession(id);
  };
</script>

<h2>{$_('cloud.security.devices.title')}</h2>
{#await cloud.getSessions()}
  <Spinner />
{:then devices}
  <Table>
    <Row>
      <Heading>OS</Heading>
      <Heading>Country</Heading>
      <Heading>IP</Heading>
    </Row>
    {#each devices as device}
      <Row on:click={() => logoutSession(device.$id)}>
        <Cell label="OS">{device.OS.name}</Cell>
        <Cell label="Country">{device.geo.country}</Cell>
        <Cell label="IP">{device.ip}</Cell>
      </Row>
    {/each}
  </Table>
{:catch error}
  <p style="color: red">{error.message}</p>
{/await}

<h2>{$_('cloud.security.logs.title')}</h2>
{#await cloud.getSecurityLog()}
  <Spinner />
{:then logs}
  <Table>
    <Row>
      <Heading>Timestamp</Heading>
      <Heading>Age</Heading>
      <Heading>Event</Heading>
    </Row>
    {#each logs as log}
      <Row>
        <Cell label="Timestamp">
          {moment(log.time, 'X').format('MMMM Do YYYY, h:mm a')}
        </Cell>
        <Cell label="Age">{moment(log.time, 'X').fromNow()}</Cell>
        <Cell label="Event">{$_(`cloud.security.logs.${log.event}`)}</Cell>
      </Row>
    {/each}
  </Table>
{:catch error}
  <p style="color: red">{error.message}</p>
{/await}
