# HTTP Mocking Helper Class(es)

Here's some code to help with mocking out your HTTP requests in Angular 2 Services. I'll probably try make an actual NPM package at some point.

# Usage

Here is an example test case: 

```
import { TestBed, inject, async, getTestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { MockBackend } from '@angular/http/testing'
import { Observable } from 'rxjs/Rx';

import { StatusService } from 'path/to/status.service';
import { HttpMockResponse } from 'path/to/mock-http-response';

describe('Status Service', () => {
  let mockBackend: MockBackend;

  beforeEach(async(() => {
    TestBed.configureTestingModule(HttpMockResponse.testModuleMetaDataWith({
      providers: [StatusService]
    }));

    mockBackend = getTestBed().get(MockBackend);
  }));
  
  it('Get the status', (done) => {
    let statusService: StatusService;

    getTestBed().compileComponents().then(() => {
          HttpMockResponse.mockHttpCalls(mockBackend, [
            HttpMockResponse.withBody({status:"submitted"})
        ]);

        statusService = getTestBed().get(StatusService);
        expect(statusService).toBeDefined();

        statusService.getStatus().subscribe( (data:any) => {
          expect(data).toBeDefined();
          expect(data.status).toBe("submitted");
          done();
        });
      });
  });
});
```

Your service might look like:

```
import { Injectable } from '@angular/core';
import { Http, Response } from "@angular/http";

@Injectable()
export class StatusService {
    constructor(private http:Http){}

    getStatus(){
        return this.http.get(`http://svc/status`).map((res:Response) => res.json());
    }
}
```