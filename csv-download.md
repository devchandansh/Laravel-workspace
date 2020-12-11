# Download CSV File Using Laravel
## @author: Chandan Sharma




# STEP-1 :: Controller Function
## 

```php
public function download_userlist( Request $request )
{
    $file_name  = "file.csv";
    
    // Get All Users List
    $query_obj = User::all();   
    
    
    
    /**
     *  Here, you can Add the Heading to the CSV File Any of the mentioned method
     *  We Can use both option-1 or Option-2 method. here we used option-2.
     *
     *  # Option-1::
     *  $csv_header = array( "Date","Time", "Username", "First Name", "Last Name", "Phone" );
     *
     *  # Option-2::
     *  $csv_header = array("DATE"=> "Date", "TIME"=>"Time", "USERNAME"=> "Username", "FIRST_NAME"=>"First Name", "LAST_NAME" =>"Last Name", "PHONE" => "Phone" );
     *
     */
    
    $csv_header = array(
                "DATE"      => "Date",
                "TIME"      => "Time",
                "USERNAME"  => "Username"
                "FIRST_NAME"=> "First Name"
                "LAST_NAME" => "Last Name"
                "PHONE"     => "Phone"
            );

    $callback = function() use($query_obj, $csv_header) 
    {
        $file = fopen('php://output', 'w');
        fputcsv($file, $csv_header);

        if(count($query_obj))
        {
            foreach ($query_obj as $key => $each) 
            {
                $row = array(
                        "DATE" => $each->date,
                        "TIME" => $each->time,
                        "USERNAME" => $each->username,
                        "FIRST_NAME" => $each->first_name,
                        "LAST_NAME" => $each->last_name,
                        "PHONE" => $each->phone,
                    );

                fputcsv($file, $row );
            }
        }

        fclose($file);
    };

    $headers = array(
          "Content-type"        => "text/csv",
          "Content-Disposition" => "attachment; filename={$file_name}",
          "Pragma"              => "no-cache",
          "Cache-Control"       => "must-revalidate, post-check=0, pre-check=0",
          "Expires"             => "0"
      );
        
    return response()->stream($callback, 200, $headers);
}
```



# STEP-2 :: Add Routes
## Add this Routes to the route file.

```php
Route::get('/download-userlist', 'UserController@download_userlist');
```



# STEP-3 :: JS File
## Add this Function to Scripts, to Download button work::

```javascript
<script>
  function downloadExcel(_this) {
      let _url = $(_this).data('href');
      window.location.href = _url;
  }
</script>
```

# STEP-4:: Add in Laravel Blade
## Add the Download Button in Blade file.

```html
<button type="button" class="button button-info" data-href="/download-userlist" onclick="downloadExcel(event.target);" ><i class="fas fa-download" ></i>Download List</button>
```



