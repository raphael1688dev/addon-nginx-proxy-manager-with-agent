# Home Assistant Community Add-on: Nginx Proxy Manager with NGINX Agent

This add-on enables you to easily forward incoming connections to anywhere,
including free SSL, without having to know too much about Nginx
or Let’s Encrypt. 

** F5 NGINX One Console Integration:**
This custom version natively integrates the **F5 NGINX Agent**. It allows you to connect your Home Assistant Nginx Proxy Manager directly to the F5 NGINX One Console for real-time traffic monitoring, metrics collection, and centralized visibility, all without breaking the standard Home Assistant experience.

Forward your domain to your Home Assistant, add-ons, or websites running
at home or anywhere else, straight from a simple, powerful interface.

Want to protect the website with a username/password? Well, it can do that too!
Enable authentication and create a list of usernames/password that can access
that specific application.

## Installation

The installation of this add-on is pretty straightforward and not different in
comparison to installing any other Home Assistant add-on.

1. Click the "Install" button to install the add-on.
2. Go to the "Configuration" tab and enter your F5 NGINX One `data_plane_key` (See the Configuration section below).
3. Start the "Nginx Proxy Manager" add-on.
4. Check the logs of the add-on to see if the NGINX Agent successfully connected to the console.
5. Click the "OPEN WEB UI" button and login using:
   `admin@example.com` / `changeme`
6. Forward port `443` (and optionally `80`) from your router to your
   Home Assistant machine.
7. Enjoy the add-on!

## Configuration

Unlike the original add-on, this custom version provides an option to enable the F5 NGINX Agent telemetry.

### Option: `data_plane_key` (Optional)
To connect this add-on to your F5 NGINX One Console, generate a Data Plane Key from your console and paste it here.

* **If provided:** The internal s6-overlay service will automatically generate the `nginx-agent.conf` and start the agent in the background to monitor your proxy hosts.
* **If left blank:** The NGINX Agent process will remain asleep. The add-on will behave exactly like the standard, unmodded Nginx Proxy Manager without consuming extra resources.

## Custom Modifications (Under the Hood)

To make the F5 NGINX Agent work seamlessly within the Home Assistant container environment, several core modifications were made to the original repository:

* **Agent Installation:** The official NGINX Agent is fetched and installed during the Docker build process.
* **Dual-Logging Mechanism:** The Home Assistant base image strictly redirects NGINX access logs to the standard output (`stdout`) for UI viewing via patches. To allow the Agent to calculate critical traffic metrics (like TPS, Latency, and HTTP error rates), the NPM backend templates were injected with `sed` commands to output a secondary, physical log strictly to `/tmp/nginx-agent-access.log`.
* **Stub Status API:** A dedicated internal server block listening on `127.0.0.1:8080` was added to expose the `stub_status` API exclusively for the Agent's basic metric collection.
* **s6-overlay Integration:** A custom startup script (`rootfs/etc/s6-overlay/s6-rc.d/nginx-agent/run`) initializes the Agent alongside the Node.js and NGINX processes, handling dynamic token injection.

## Changelog & Releases

This repository keeps a change log using [GitHub's releases][releases]
functionality.

Releases are based on [Semantic Versioning][semver], and use the format
of `MAJOR.MINOR.PATCH`. In a nutshell, the version will be incremented
based on the following:

- `MAJOR`: Incompatible or major changes.
- `MINOR`: Backwards-compatible new features and enhancements.
- `PATCH`: Backwards-compatible bugfixes and package updates.

## Support

Got questions?

You have several options to get them answered:

- The [Home Assistant Community Add-ons Discord chat server][discord] for add-on
  support and feature requests.
- The [Home Assistant Discord chat server][discord-ha] for general Home
  Assistant discussions and questions.
- The Home Assistant [Community Forum][forum].
- Join the [Reddit subreddit][reddit] in [/r/homeassistant][reddit]

You could also [open an issue here][issue] GitHub.

## Authors & contributors

The original setup of this repository is by [Franck Nijhof][frenck].

For a full list of all authors and contributors,
check [the contributor's page][contributors].

## License

MIT License

Copyright (c) 2019-2025 Franck Nijhof

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

[addon-badge]: https://my.home-assistant.io/badges/supervisor_addon.svg
[addon]: https://my.home-assistant.io/redirect/supervisor_addon/?addon=a0d7b954_nginxproxymanager&repository_url=https%3A%2F%2Fgithub.com%2Fhassio-addons%2Frepository
[contributors]: https://github.com/hassio-addons/addon-nginx-proxy-manager/graphs/contributors
[discord-ha]: https://discord.gg/c5DvZ4e
[discord]: https://discord.me/hassioaddons
[forum]: https://community.home-assistant.io/t/home-assistant-community-add-on-nginx-proxy-manager/111830?u=frenck
[frenck]: https://github.com/frenck
[issue]: https://github.com/hassio-addons/addon-nginx-proxy-manager/issues
[reddit]: https://reddit.com/r/homeassistant
[releases]: https://github.com/hassio-addons/addon-nginx-proxy-manager/releases
[semver]: https://semver.org/spec/v2.0.0.html
