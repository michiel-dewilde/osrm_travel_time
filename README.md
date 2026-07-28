# OSRM Travel Time sensor for Home Assistant (osrm_travel_time)

> **Fork notice.** This is a maintenance fork of
> [edekeijzer/osrm_travel_time](https://github.com/edekeijzer/osrm_travel_time)
> (all credit to the original author). It carries small fixes to keep the
> platform working on current Home Assistant cores:
> - Replace constants removed from `homeassistant.const`
>   (`TIME_MINUTES` → `UnitOfTime.MINUTES`, and the removed
>   `CONF_UNIT_SYSTEM_IMPERIAL` / `CONF_UNIT_SYSTEM_METRIC`), which otherwise
>   make the platform fail to import.
> - Replace the removed `hass.config.units.name` with a `METRIC_SYSTEM` check.
> - Use raw-string regex literals (silences `SyntaxWarning: invalid escape
>   sequence`).
>
> It also adds one feature: **event-driven updates**. When origin/destination
> are entities, the sensor recomputes on each of their state changes (e.g. every
> GPS position update) throttled by the new `min_update_interval` option
> (default 5 s), instead of only polling on `scan_interval`.
>
> Behaviour is otherwise identical to upstream.

This plugin is heavily based on https://github.com/eifinger/open_route_service and modified to use the https://pypi.org/project/osrm-py/ client for OSRM for making completely self-contained travel times in possible in Home Assistant.
You can specify origin and destination by either a device_tracker, zone or person entity_id or by latitude/longitude coordinates.

![screenshot](screenshot.png)

## Configuration
```yaml
sensor:
  - platform: osrm_travel_time
    name: Travel home by bike
    server: 'https://route.example.com'
    profile: bike
    origin_entity_id: person.my_person
    destination_latitude: '52.0906894'
    destination_longitude: '5.1213124'
  - platform: osrm_travel_time
    name: Travel home by car
    server: 'http://localhost:5000'
    profile: car
    origin_entity_id: device_tracker.my_device
    destination_entity_id: zone.home
```

## Configuration options
Key | Type | Required | Description
-- | -- | -- | --
`server` | `string` | `true` | Your OSRM server without path. The `profile` option will be included in the path.
`origin_entity_id` | `string` | `true` | The *entity_id* used as the starting point for calculating travel distance and time. Cannot be used in combination with `origin_latitude` / `origin_longitude`.
`origin_latitude` | `string` | `true` | The latitude of the starting point for calculating travel distance and time. Must be used in combination with `origin_longitude`. Cannot be used in combination with `origin_entity_id`.
`origin_longitude` | `string` | `true` | The longitude of the starting point for calculating travel distance and time. Must be used in combination with `origin_latitude`. Cannot be used in combination with `origin_entity_id`.
`origin_name` | `string` | `false` | Display name for the origin. If entity is used as origin, its name will be shown unless this option is specified.
`destination_entity_id` | `string` | `true` | The *entity_id* used as the end point for calculating travel distance and time. Cannot be used in combination with `destination_latitude`/`destination_longitude`.
`destination_latitude` | `string` | `true` | The latitude of the end point for calculating travel distance and time. Must be used in combination with `destination_longitude`. Cannot be used in combination with `destination_entity_id`.
`destination_longitude` | `string` | `true` | The longitude of the end point for calculating travel distance and time. Must be used in combination with `destination_latitude`. Cannot be used in combination with `destination_entity_id`.
`destination_name` | `string` | `false` | Display name for the destination. If entity is used as destination, its name will be shown unless this option is specified.
`name` | `string` | `false` | A name to display on the sensor. The default is *OSRM Travel Time*.
`profile` | `string` | `false` | Enter a profile name here, which exists in your OSRM server. The default is *car*.
`unit_system` | `string` | `false` | You can choose between `metric` or `imperial`. The default is to follow your Home Assistant configuration.
`scan_interval` | `integer` | `false` | "Defines the periodic (fallback) update interval of the sensor in seconds. Defaults to *300* (5 minutes)."
`min_update_interval` | `integer` | `false` | When `origin_entity_id`/`destination_entity_id` are used, the sensor also recomputes on each of their state changes (e.g. every GPS position update), throttled to at most once per this many seconds. Defaults to *5*. Set to *0* to disable event-driven updates and rely only on `scan_interval`.

## Roadmap
Things I'd like to add
- [ ] Create config flow instead of configuring through YAML
- [ ] Fix slow initial update when entity is a `device_tracker`
- [ ] Add/improve documentation on configuring OSRM

## Bugs and support
If you find a bug or have a feature request, please let me know in the issue tracker or fork the plugin, add your feature and create a Pull Request. Please note that I do not provide any support on OSRM deployment or configuration, please see the [OSRM wiki](https://github.com/Project-OSRM/osrm-backend/wiki) for that.
