# LaunchDarkly => Statsig Migration Script Template

This script is designed to help automate some of the migration of feature flags from LaunchDarkly to Statsig. It fetches feature flags from LaunchDarkly, translates them into Statsig's format, and creates corresponding feature gates in Statsig. Additionally, it tracks the migration status and details in a CSV file.

## Considerations

This script should work out of the box. I'd suggesting getting started with a test environment of 5-10 flags. However, before running the script on a large scale, consider the following:

- __IMPORTANT__: If you don't want/need to customize this import script, you may consider using [Statsig's built in LaunchDarkly migration tool](https://docs.statsig.com/guides/migrate-from-launchdarkly).
- The script migrates boolean flags from LaunchDarkly. Non-boolean flags require manual migration.
- The script uses a tag (`Migration Script`) to identify migrated flags in Statsig. Ensure this tag is unique and recognizable.
- The script includes a function to delete all Statsig feature gates with a specific tag. Use this with caution to clean up after a test or failed migration.
- The script maps LaunchDarkly operators to Statsig operators. Review and adjust the `mapOperator` function to ensure correct mapping.
- The script maps LaunchDarkly attributes to Statsig condition types. Customize the `mapType` function to match your attributes.
- The script calculates the pass percentage for Statsig rules based on LaunchDarkly's rollout weights or variation settings.
- The script requires API keys for both LaunchDarkly and Statsig, which should be kept secure.
- __Environments__: In Statsig, the hierarchy is designed with a single project that contains multiple environments, such as Development, Staging, and Production. Conversely, LaunchDarkly adopts an Environment > Project hierarchy, where each environment can be considered a separate project with its own set of feature flags. Adjust the environment logic in this script accordingly. (Open to feedback/pull requests on how to better handle this.) Here's an example of a flag which is only on in development, which was imported using the current migration script: ![Untitled-1](https://github.com/statsig-io/launchDarkly_migration_script_template/assets/5475308/0368679e-d3b1-4370-94c3-2637c71b961f)

- __Defaults__: Currently, Statsig feature gates will always default to false. No default rules will be migrated from LaunchDarkly in the current iteration of this script. 

## Installation

To run the script, you need Node.js and npm installed on your system. Follow these steps to set up the script:

1. Clone or download the script to your local machine.
2. Navigate to the script's directory in your terminal.
3. Run `npm install` to install the required dependencies, including `axios` for HTTP requests and `csv-writer` for CSV file operations.

## Configuration

Before running the script, you must set up the following environment variables:

- `LAUNCHDARKLY_API_KEY`: Your LaunchDarkly API key.
- `LAUNCHDARKLY_PROJECT_KEY`: The project key in LaunchDarkly (default is "default").
- `LAUNCHDARKLY_ENVIRONMENT`: The LaunchDarkly environment from which which you want to migrate the flags (e.g., "production").
- `STATSIG_API_KEY`: Your Statsig console API key.

Replace the placeholder values (`"XXXXXXXX"`) with your actual API keys.

## Running the Script

To execute the migration script, run the following command in your terminal:

```bash
node index.js
```

The script will perform the following actions:

1. Fetch all feature flags from LaunchDarkly.
2. Translate each flag into Statsig's format.
3. Create feature gates in Statsig.
4. Write the migration status and details to a CSV file named `flag_migration_tracker.csv`.

## Example Translations

This LaunchDarkly feature flag:

![Untitled](https://github.com/statsig-io/launchDarkly_migration_script_template/assets/5475308/6ae5f8fd-a1d0-4b2e-9d55-a69122e73532)

Would be translated to this Statsig feature gate:

<img width="937" alt="Untitled" src="https://github.com/statsig-io/launchDarkly_migration_script_template/assets/5475308/92e92a1c-bf55-48c6-9074-0cf4da273bd1">

And this LaunchDarkly feature flag:

![Untitled](https://github.com/statsig-io/launchDarkly_migration_script_template/assets/5475308/f60c1331-cca4-4688-ab0d-f01515a791a0)

Would be translated to this Statsig feature gate:

<img width="938" alt="Untitled" src="https://github.com/statsig-io/launchDarkly_migration_script_template/assets/5475308/3cf59ecb-6e8f-4eb7-806e-e369236134f1">

## Output

The script outputs a CSV file with the following columns:

- `migration_status`: The status of the migration for each flag.
- `ld_flag_name`: The name of the flag in LaunchDarkly.
- `statsig_gate_name`: The name of the gate in Statsig.
- `ld_url`: The URL to the flag in LaunchDarkly.
- `statsig_url`: The URL to the gate in Statsig.
- `ld_flag_key`: The key of the flag in LaunchDarkly.
- `statsig_id`: The ID of the gate in Statsig.
- `ld_creation_date`: The creation date of the flag in LaunchDarkly.
- `statsig_created_time`: The creation time of the gate in Statsig.

<img width="953" alt="Untitled" src="https://github.com/statsig-io/launchDarkly_migration_script_template/assets/5475308/24232bf3-f853-4684-a9ce-6d7a0d98aed6">

You can use this to help track the migration and easily reference URLs of gates in either platform and their migration status.

## Troubleshooting

If you encounter issues during the migration, check the following:

- Ensure that the API keys are correct and have the necessary permissions.
- Verify that the `TAG_NAME` is unique and does not conflict with existing tags in Statsig.
- Review the error messages in the console for clues on what might have gone wrong.
- Check the CSV output file for the migration status of each flag.

Pull requests and feedback welcome!

