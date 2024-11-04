# Tag-Based Time Tracker 

![A screenshot of the plugin in action](reporting-screenshot.png)

This is a fork of [super-simple-plugin](https://github.com/Ellpeck/ObsidianSimpleTimeTracker) that has support for out-of-the-box tagging.

# TLDR 

1. Execute the `Tag Based Time Tracker: Insert Time Tracker` command to start logging.
2. Set your tags in the settings in YAML format:

```yaml
streams:
  name: "🌊 Streams"
  items:
    - topic: "Accounting"
      icon: "🧮"
      tag: "#tt_accounting"
      subTags: []
    - topic: "Development"
      icon: "💗"
      tag: "#tt_dev"
```

3. Execute `Insert Time Tracking Summary` to get a summary for the desired time interval.

# Description
A time tracker is essentially a special code block that stores information about the times you pressed the Start and End buttons. Since time is tracked solely through timestamps, you can switch notes, close Obsidian, or even shut down your device completely while the tracker is running! When you come back, your time tracker will still be running.

The tracker's information is stored in the code block as JSON data. The names, start times, and end times of each segment are stored and displayed neatly in the code block in preview or reading mode.

# Time Tracking Summary / Reporting

1. **Time Tracking Entries with Tags**: Track work sessions with tags to categorize different activities.
    
    - Example of an entry: `#tt_dev #tt_client_a #tt_frontend` represents time spent working on frontend development for a specific client.
        
2. **Enhanced Reporting Functionality**: Generate time tracking reports for specific time periods, allowing detailed insight into how time was allocated.

    - **Topic-based Reports**: View summaries of time spent based on specific topics, such as Development, Accounting, etc.
    - **Multiple Parallel Topics Possible**: Track multiple dimensions.
    - **Subtags**: Track specific tags and group time tracking summaries in a markdown file. This will generate a report for a given period, optionally filtered by a specific topic.

```yaml
# You can have as many 'sections' as you want to track different domains separately or in parallel

# Example section / topic 1
streams:
  name: "🌊 Streams"
  items:
    - topic: "Accounting"
      icon: "🧮"
      tag: "#tt_accounting"
      subTags: []

    - topic: "Development"
      icon: "💗"
      tag: "#tt_dev"
      subTags:
        - topic: "Frontend"
          tag: "#tt_frontend"
          subTags: []

        - topic: "Backend"
          tag: "#tt_backend"
          subTags: []

# Example section / topic 2
clients: 
  name: "👨🏼‍💼 Clients"
  items:
    - topic: "Client A"
      tag: "#tt_client_a"
      subTags: []

    - topic: "Client B"
      tag: "#tt_client_b"
      subTags: []
```

## Tracker Data in Dataview
Tag Based Time Tracker has a public API that can be used with [Dataview](https://blacksmithgu.github.io/obsidian-dataview/), specifically [DataviewJS](https://blacksmithgu.github.io/obsidian-dataview/api/intro/), which can be accessed using the following code:

```js
dv.app.plugins.plugins["tag-based-time-tracker"].api;
```

The following is a short example that uses DataviewJS to load all trackers in the vault and print the total duration of each tracker:

```js
// Get the time tracker plugin API instance
let api = dv.app.plugins.plugins["tag-based-time-tracker"].api;

for (let page of dv.pages()) {
    // Load trackers in the file with the given path
    let trackers = await api.loadAllTrackers(page.file.path);

    if (trackers.length)
        dv.el("strong", "Trackers in " + page.file.name);

    for (let { section, tracker } of trackers) {
        // Print the total duration of the tracker
        let duration = api.getTotalDuration(tracker.entries);
        dv.el("p", api.formatDuration(duration));
    }
}
```
