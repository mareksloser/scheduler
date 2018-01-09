# Scheduler

Small lib for executing php callbacks using cron expression.

## Configuration

Register extension.

```yaml
extensions:
    scheduler: Contributte\Scheduler\DI\SchedulerExtension
```

Set-up crontab. Use `scheduler:run` command.

```
* * * * * php path-to-project/console scheduler:run
```

Optionally you can set temp path for lock files.

```yaml
scheduler:
    path: '%tempDir%/scheduler'
```

## Jobs

### Callback job

Set cron expression and php callback.

```yaml
scheduler:
    jobs:
        - {cron: '* * * * *', callback: App\Model\Pirate::arrgghh}
        - {cron: '*/2 * * * *', callback: App\Model\Parrot::echo}
```

Cron expression:

    *    *    *    *    *
    -    -    -    -    -
    |    |    |    |    |
    |    |    |    |    |
    |    |    |    |    +----- day of week (0 - 7) (Sunday=0 or 7)
    |    |    |    +---------- month (1 - 12)
    |    |    +--------------- day of month (1 - 31)
    |    +-------------------- hour (0 - 23)
    +------------------------- min (0 - 59)

### Custom job

Use `IJob` interface. Every job is registered as service in DIC, so you can use other services.

```php

class MyAwesomeJob implements IJob
{

	/** @var Database */
	private $database;
	
	/**
	 * @param Database $database
	 */
	public function __construct(Database $database)
	{
		$this->database = $database;
	}

	/**
	 * @param DateTime $dateTime
	 * @return bool
	 */
	public function isDue(DateTime $dateTime)
	{
		//$this->database->...
		return TRUE; // When is job ready to run
	}

	/**
	 * @return void
	 */
	public function run()
	{
		// Do something
	}

}

```

And don't forget register it.

```yaml
scheduler:
    jobs:
        - App\Model\MyAwesomeJob
```

## Commands

### Help

Print cron syntax.

```
scheduler:help
```

### List

List all jobs.

```
scheduler:list
```

### Run

Run all due jobs.

```
scheduler:run
```
