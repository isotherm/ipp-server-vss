# IPP Server for Visionect / Joan e-ink devices

This uses the Visionect / Joan e-ink tablet API to expose the tablet as a network printer over IPP. This allows easily updating the tablet with anything that can be printed (e.g. selection of a web page, page from a PDF file, an image, etc).

## Use Cases

This script was created specifically for printing recipes to an e-ink tablet magnetically attached to a refrigerator door. This is designed to run on a Raspberry Pi, together with the VSS Docker container. However, they could be on separate systems.

## Setup

1. The script depends on `ippserver`, `vssapi`, and `pdf2image`. The latter needs the `pdftoppm` and `pdftocairo` utilities, often included with `poppler`. On Debian-based systems, the dependencies may be installed as below:  
```
sudo apt install poppler-utils
pip install pdf2image ippserver vssapi
```
2. The service file should be copied into `/etc/systemd/system/`, chown root:root and chmod 755. Edit paths and usernames as necessary (because this was designed to run on a Raspberry Pi). Use `sudo systemctl daemon-reload` to process the file.
3. If you haven't already, log in to the VSS web interface, go to the Users tab, and click "Add new API key". You'll need it for step #4. Also get the UUID for the target device, and be sure it is set to HTTP backend under "Advanced settings."
4. Run `sudo systemctl edit ipp-server-vss` and paste the below in, substituting the values for your VSS instance and the device UUID.
```
[Service]
Environment="VSS_API_URL=http://example.com:8081/"
Environment="VSS_API_KEY=XXX_API_KEY_HERE_XXX"
Environment="VSS_API_SECRET=XXX_API_SECRET_HERE_XXX"
Environment="VSS_DEVICE_UUID=XXX_DEVICE_UUID_HERE_XXX"
```
5. Start the service with `sudo systemctl start ipp-server-vss` and then verify it is running with `sudo systemctl status ipp-server-vss`.
6. On any computer(s) that want to print, add the printer using ipp://1.2.3.4:2031/ipp/ (substituting your server's IP address) and select the Generic PDF printer/driver. Print a test page to verify operation.

## Future

It would be nice to support all available devices via URIs, but this would require forking `ippserver` as it does not support this functionality.
