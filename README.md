# Configuration

Since many users can not or do not want to edit the plugin directly to change settings or options, it is best to offer them a configuration file that can easily be edited without worrying about messing up the plugin or it resetting each time the plugin is updated. New configuration values that are added during updates should automatically set a default value and not a null value.

# Simple Configuration
For a more complete solution please see the Advanced Configuration section below. The simple configuration setup has many drawbacks including the inability to easily introduce new configuration properties or adjust default values across plugin updates.

# Creating
There are many approaches to creating and managing a configuration file. This example shows the simplest usage. If your configuration file doesn't exist `LoadDefaultConfig()` will be called. You can define your configuration properties here. The `Config` object is provided by oxide and does not need to be redefined in this simple usage case. Set the properties of the `Config` object:
```
protected override void LoadDefaultConfig()
{
    LogWarning("Creating a new configuration file");
    Config["ShowJoinMessage"] = true;
    Config["ShowLeaveMessage"] = true;
    Config["JoinMessage"] = "Welcome to this server";
    Config["LeaveMessage"] = "Goodbye";
}
```

# Reading properties
To read from the configuration and access the values within you can directly read properties from the `Config` object that you previously set.
```
[Command("test")]
private void TestCommand(IPlayer player, string command, string[] args)
{
    if (Config["ShowJoinMessage"])
        player.Message($"{Config["JoinMessage"]}")
}
```

# Advanced Configuration
The following approach to managing your configuration file offers many benefits over the simple configuration method. Using this approach you will be able to create, modify/update, and remove configuration properties across plugin updates without having to worry about the configuration file resetting or breaking. You can also specify default values for new configuration properties that will be automatically set.

# Create configuration class
Define your configuration class and set the default values here.
```
private PluginConfig config;

private class PluginConfig
{
	public bool ShowJoinMessage = true;
	public bool ShowLeaveMessage = true;
	public bool JoinMessage = "Welcome to this server";
	public bool LeaveMessage = "Goodbye";
}

```

# Loading configuration from file
Read the saved configuration file or load the default config if the file doesn't exist yet. After reading the config, save it again to apply any new updates.
```
protected override void LoadConfig()
{
    try
    {
        base.LoadConfig();
        config = Config.ReadObject<PluginConfig>();
        SaveConfig();
    }
    catch (Exception ex)
    {
        LoadDefaultConfig();
    }
}
```

# Load default configuration
Set your default config if it hasn't been created yet.
```
protected override void LoadDefaultConfig()
{
        LogWarning("Creating a new configuration file");
	config = new PluginConfig();
}
```

# Saving configuration
Write our `config` object to the configuration file.
```
private void SaveConfig() => Config.WriteObject(config, true);
```

# Extended configuration management and additional classes
Using the advanced configuration method you can easily extend your configuration file with custom classes and properties across versions of your plugin.
```
private PluginConfig config;

private class SettingsGroup
{
	public bool moreSettings1 = false;
	public float moreSettings2 = 3.05f;
    	public string moreSettings3 = "Advanced Configuration";
}

private class PluginConfig
{
	public bool ShowJoinMessage = true;
	public bool ShowLeaveMessage = true;
	public bool JoinMessage = "Welcome to this server";
	public bool LeaveMessage = "Goodbye";
	public SettingsGroup specificSettings = new SettingsGroup();
}
```

# Full Advanced Configuration Example
```
private PluginConfig config;

private class PluginConfig
{
	public bool ShowJoinMessage = true;
	public bool ShowLeaveMessage = true;
	public bool JoinMessage = "Welcome to this server";
	public bool LeaveMessage = "Goodbye";
}

protected override void LoadDefaultConfig()
{
        LogWarning("Creating a new configuration file");
	config = new PluginConfig();
}

protected override void LoadConfig()
{
    try
    {
        base.LoadConfig();
        config = Config.ReadObject<PluginConfig>();
        SaveConfig();
    }
    catch (Exception ex)
    {
        LoadDefaultConfig();
    }
}

private void SaveConfig() => Config.WriteObject(config, true);
```
