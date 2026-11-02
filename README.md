# Faith-Based Health Facilities Map

An interactive map of 605 hospitals with search, filters, nearest-hospital lookup, distance measuring, distance exports and proximity grouping. Everything runs in the visitor's browser (Leaflet + Turf.js), so there is no server or database to maintain.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app: map, tools, styling and logic |
| `hospitals.csv` | The hospital list the map loads on start |
| `README.md` | This guide |

## Publish on GitHub Pages (about 5 minutes, no coding)

1. Sign in at github.com, click **+ → New repository**. Name it e.g. `hospital-map`, set it to **Public**, and click **Create repository**.
2. On the new repo page click **uploading an existing file**, drag in `index.html`, `hospitals.csv` and `README.md`, then click **Commit changes**.
3. Go to **Settings → Pages**. Under *Build and deployment* choose **Deploy from a branch**, branch **main**, folder **/ (root)**, then **Save**.
4. After 1–2 minutes the page shows your link: `https://<your-username>.github.io/hospital-map/`. Share that link — anyone can open it on a phone or computer, with no login.

> A free GitHub Pages site is public. Anyone with the link can see the hospital list. If the list must stay private, use a private host instead (for example Netlify with password protection, or your organisation's server).

## Updating the hospital list

Replace `hospitals.csv` in the repo (**Add file → Upload files**, same name) and commit. The site updates within a minute or two.

The CSV needs at least `Latitude` and `Longitude` columns in decimal degrees. A name column (`Hospital Name`, `Name` or `Facility`) is used for labels. These columns become filters automatically when present: `Region`, `State`, `LGA`, `Denomination`, `Location precision`. Any other column is shown in the popup.

Visitors can also load their own CSV, Excel or GeoJSON file from the **Data** tab (or by dragging it onto the map). That only affects their own browser.

### Pulling coordinates from DHIS2
Export facilities as GeoJSON from the DHIS2 Maps app, or use the API:
`https://<your-dhis2>/api/organisationUnits.geojson?level=<facility level>`
then load the file in the Data tab, or convert it to `hospitals.csv`.

## Road distance and driving time (optional)

Straight-line distance works out of the box. For road distance:
1. Create a free account at https://openrouteservice.org/dev/#/signup and copy your API key.
2. Either paste it in the **Data** tab (saved only in that visitor's browser), or
3. set it for everyone by editing `index.html`: find `orsKey: ''` near the top and put the key between the quotes.
   Note that anyone can read a key placed in the page, and the free plan has daily limits shared by all visitors.

## Background maps

There are four background maps, none of which needs an API key: OpenStreetMap streets (the default), Esri Streets, Esri Topographic and Esri Satellite. The page remembers each visitor's choice.

OpenStreetMap's volunteer servers block requests that don't say which website they come from (the Referer). That's why opening `index.html` straight from your computer (a `file://` address) shows an "Access blocked" tile. In that case the page starts on Esri Streets instead. On GitHub Pages, OpenStreetMap works normally.

## Customising

All settings are in the `CFG` block at the top of the `<script>` in `index.html`: title, data file name, which columns are filters, default colour field, and the bounding box used to flag suspicious coordinates.

## How the tools work

- **Find:** search by name, town, LGA or state. Click a result to zoom to it.
- **Nearest:** use your location (needs the HTTPS GitHub Pages link and location permission), pick a point, or click *Nearest from here* in a hospital's popup. You can show the top N or every hospital within a radius.
- **Distance:** measure between any two hospitals or points. Export pairs within X km, each hospital's nearest neighbour, or a full distance matrix as CSV.
- **Groups:** three ways to group hospitals:
  - *Max distance* (complete-linkage clustering): every pair of hospitals in a group is within X km of each other.
  - *Neighbour chain* (DBSCAN): hospitals within X km of any group member join the group.
  - *Fixed number* (k-means): exactly N groups.
  You can download the groups as CSV.
- **Data:** checks coordinates, flags hospitals that share identical points (usually LGA/State estimates), and loads other files.

## Data notes

According to its `Location precision` column, about a third of this list (204 + 4 rows) is matched exactly, 328 rows are approximate or probable, and 69 rows are only LGA/State estimates. Several estimated hospitals share the same point, so their distances are rough and they can group together artificially. The Groups tab has an option to leave them out.
