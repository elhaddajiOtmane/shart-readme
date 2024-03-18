```mermaid
  flowchart TD
    Request(Request) -->schema-related{Is it requesting Schemas?}
    schema-related -->|No| req_no_schemas[Let the original request go through]
    req_no_schemas --> Response(Response)
    schema-related -->|Yes| req_schemas[Fetch from GitHub]
    req_schemas-->req_json{Was requesting a .json file?}
    
    req_json -->|No| Response(Response)
    req_json -->|Yes| response_status{Response Status?}
    
    response_status -->|2xx| response_ok[OK]
    response_status -->|304| response_cached[Not Modified]
    response_status -->|Any other| response_ko

    response_ok --> set_headers[Set Response Content-Type header to application/schema+json]
    set_headers --> send_metric_success[Send success metric]
    response_cached -- cached:true --> send_metric_success
    response_ko --> send_metric_error[Send error metric]

    send_metric_success -- file served from raw GH --> Response(Response)
    send_metric_error --the errored response --> Response(Response)
  ```
