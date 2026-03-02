# Web Dashboard

A lightweight dashboard built specifically for resource-constrained devices.  

By leveraging native Web Components and Server-Sent Events (SSE), this project delivers a real-time reactive UI with a minimal memory footprint. Making it ideal for being served directly from a device’s Flash memory (e.g., ESP32).

## Tech Stack

- [Web Components](https://developer.mozilla.org/en-US/docs/Web/API/Web_components): Modular, encapsulated UI elements using standard Browser APIs.

- [SASS](https://sass-lang.com/): Advanced styling compiled into optimized CSS.

- [Webpack](https://webpack.js.org/):Bundles and minifies the entire project into a single compact JavaScript file for embedded deployment.

## Real-Time Communication

This dashboard uses a single Server-Sent Events (SSE) endpoint exposed by the device.

Instead of creating multiple endpoints for each GPIO or analog channel, the ESP32 provides one centralized event stream (e.g., /api/events). All hardware updates are pushed through this single connection.

Each Web Component subscribes to this stream and internally filters the events based on its num attribute.

## Development

### 1. Install dependencies

Ensure you have [pnpm](https://pnpm.io/) installed:

```bash
$ pnpm install
```

### 2. Development Environment

Run the Webpack Dev Server along with a Mock API (Express) that emulates the ESP32 REST endpoints:

```bash
$ pnpm run dev
```

### 3. Production Build

Generate the optimized files for deployment to your microcontroller:

```bash
$ pnpm run build
```

Output files:

- `index.html`: The entry point.
- `bundle.js`: A single minified file containing all logic, styles, and components.

## Component Library

The UI is built using a custom library of HTML tags powered by Web Components.

All hardware-related components subscribe to a single global SSE endpoint exposed by the device and internally filter events based on their configuration.

### Layout & Navigation

These components define the application structure and handle page routing.

They are purely structural and do not interact with the hardware event stream.

| Tag                  | Description                                                            |
|----------------------|------------------------------------------------------------------------|
| `<dashboard-struct>` | Root container that initializes the layout                             |
| `<dashboard-menu>`   | Sidebar or navigation bar container                                    |
| `<dashboard-option>` | A clickable menu item. Uses the `target` attribute to switch pages     |
| `<dashboard-content>`| Viewport manager. Shows the page matching the current selection        |
| `<dashboard-page>`   | A content container identified by a unique `name`                      |

#### Example Usage

```html
<dashboard-struct>
  <dashboard-menu>
    <dashboard-option target="home">Home</dashboard-option>
    <dashboard-option target="sensors">Sensors</dashboard-option>
  </dashboard-menu>

  <dashboard-content default="home">
    <dashboard-page name="home">
      <h1>Device Status</h1>
    </dashboard-page>

    <dashboard-page name="sensors">
      <h1>Real-time Data</h1>
    </dashboard-page>
  </dashboard-content>
</dashboard-struct>
```

### Hardware I/O Components

All hardware components:
  
  - Connect to the same SSE event stream
  - Listen to incoming events

Update only when:

  - The event type matches (digital or analog)
  - The event num matches their num attribute

This ensures a single HTTP connection, minimal memory usage on the device, and a scalable frontend architecture.

### Digital Signals (Binary State – ON/OFF)

Used for GPIO inputs and outputs.

| Tag                       | Description                                                                                     |
|---------------------------|-------------------------------------------------------------------------------------------------|
| `<digital-input-element>` | Read-only representation of a specific GPIO input. Listens to digital events matching its `num` |
| `<digital-output-element>`| Interactive switch that controls a GPIO output and listens for state updates for its `num`      |

#### Example Usage

```html
<digital-input-element num="0">Motion Sensor</digital-input-element>
<digital-output-element num="2">Main Relay</digital-output-element>
```

### Analog Signals

Used for range-based values such as temperature, humidity, or voltage readings.

| Tag                      | Description                                                                           |
|--------------------------|---------------------------------------------------------------------------------------|
| `<analog-input-element>` | Listens to analog events from the global SSE stream and updates only when num matches |


#### Example Usage

```html
<analog-input-element num="0" min="0" max="4096">Analog 0</analog-input-element>
<analog-input-element num="1" min="0" max="4096">Analog 1</analog-input-element>
<analog-input-element name="Test1" num="2" min="0" max="50">Temperature</analog-input-element>
<analog-input-element name="Test1" num="3" min="0" max="100">Humidity</analog-input-element>
```

### Icon

Used for rendering SVG icons bundled within the project.

| Tag                  | Description                                          |
|----------------------|------------------------------------------------------|
| `<icon-element>`     | Renders the SVG icon defined by the `name` attribute |


#### Example Usage

```html
<icon-element name="sort-horizontal"></icon-element>
<icon-element name="tunning"></icon-element>
```

### Image

Used for rendering static images bundled with the dashboard.

| Tag                  | Description                                     |
|----------------------|-------------------------------------------------|
| `<image-element>` | Renders the image defined by the `name` attribute  |


#### Example Usage

```html
<image-element name="utfpr"></image-element>
```

## Gallery

Below are some screenshots demonstrating the dashboard running on both desktop and mobile devices.

### Desktop

<img width="1920" height="909" alt="Captura de tela 2026-03-02 150505" src="https://github.com/user-attachments/assets/b937d683-d8c8-40a2-a11d-1c9694c545a6" />
<img width="1920" height="909" alt="Captura de tela 2026-03-02 150518" src="https://github.com/user-attachments/assets/05075851-90c8-4d2b-aee3-b52b8a23d1c3" />

### Mobile  

<img width="300" height="822" alt="Captura de tela 2026-03-02 150534" src="https://github.com/user-attachments/assets/6533ce32-70fe-4a0e-83ff-fba97b3de3e0" />
<img width="300" height="822" alt="Captura de tela 2026-03-02 150539" src="https://github.com/user-attachments/assets/514ac0fe-d098-468c-bc0d-361ff8a73c15" />
<img width="300" height="822" alt="Captura de tela 2026-03-02 150547" src="https://github.com/user-attachments/assets/740ae5f6-d068-4bcb-beda-f89c10da91f3" />
