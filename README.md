# Laravel Queue Worker with nohup

This guide explains how to run Laravel queue workers using `nohup` to keep them running in the background, even after closing the terminal session.

## Prerequisites
- A server with Laravel installed
- SSH access to the server
- Supervisor access (optional for better process management)

## Installing and Running Laravel Queue Worker with nohup

Run the following command to start a queue worker using `nohup`:

```sh
nohup php artisan queue:work --tries=3 --timeout=90 > storage/logs/queue.log 2>&1 &
```

### Explanation:
- `nohup` ensures the process keeps running even after logging out.
- `php artisan queue:work` starts the queue worker.
- `--tries=3` limits the number of times a job will be retried on failure.
- `--timeout=90` sets the maximum execution time for a job.
- `> storage/logs/queue.log 2>&1 &` redirects output to a log file and runs the process in the background.

## Checking Running Queue Workers
To check if the queue worker is running, use:

```sh
ps aux | grep queue:work
```

## Stopping Laravel Queue Worker
To stop the queue worker, find its process ID and kill it:

```sh
ps aux | grep queue:work
kill -9 <PID>
```
Replace `<PID>` with the actual process ID from the previous command.

## Alternative: Using Supervisor (Recommended)
For better process management, consider using Supervisor:
1. Install Supervisor:
   ```sh
   sudo apt install supervisor
   ```
2. Create a new Supervisor configuration file:
   ```sh
   sudo nano /etc/supervisor/conf.d/laravel-queue.conf
   ```
3. Add the following content:
   ```ini
   [program:laravel-queue]
   process_name=%(program_name)s_%(process_num)02d
   command=php /path/to/artisan queue:work --tries=3 --timeout=90
   autostart=true
   autorestart=true
   numprocs=1
   redirect_stderr=true
   stdout_logfile=/path/to/storage/logs/queue.log
   ```
4. Reload Supervisor:
   ```sh
   sudo supervisorctl reread
   sudo supervisorctl update
   sudo supervisorctl start laravel-queue
   ```

## Notes
- Always run queue workers in a managed way, either with `nohup` or Supervisor.
- Logs can be checked in `storage/logs/queue.log` for debugging.

## Future Enhancements
- Consider using a queue monitoring tool like Laravel Horizon for better visibility.
- Implement Supervisor for persistent queue management.
- Automate worker restarts in case of crashes.

---

This repository is maintained for future solutions and improvements. Feel free to contribute!

