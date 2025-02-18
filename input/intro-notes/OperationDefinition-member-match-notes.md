
The response from a failed $member-match is a "422 Unprocessable Entity Status Code".

After a successful $member-match the requesting system **SHALL** then use the UMB provided by the target payer in the `Patient.identifier` field in any subsequent transactions with the same system.  If the requesting system was a payer with coverage for the member, the receiving system SHOULD create a linkage between their own member information and the Coverage provided by the requesting system.  This linkage can be used to support subsequent queries by establishing a known 'reason' for accessing a member's information.

For example, in the Da Vinci PDex IG, the requesting system will subsequently use the UMB identifier to request the member’s health records. This can be done by querying the US Core FHIR profile endpoints which will be constrained to the identified member. Alternatively, the requesting can perform a $everything operation to the Patient/{ID}/$everything operation endpoint to receive a bundle of the member’s health records.

For PCDE, the requesting system will subsequently use the UMB identifier to send a Task message and request the PCDE coverage transition bundle.

Similarly provider systems that initiate a member match can use the UMB in subsequent queries to access payer-held clinical and other information.

NOTE: For privacy reasons, the 'CoverageToLink' **SHOULD NOT** include any data elements not marked as mustSupport in the Coverage profile.


#### Resolving parameter references
The input parameters include a Coverage resource with a reference to a Patient resource.  That reference **SHALL** be a 'local' reference (i.e. starting with "Patient/" rather than "http"), **SHALL** be resolved to the parameter with the name "MemberPatient" and **SHALL** refer to the same id.

#### Member matching Logic
This specification does not define the member matching logic that is used by a Payer that processes a $member-match operation.  However, because matching member identity is a key step in the release of potentially sensitive patient information, the algorithms used should be robust.  Servers **SHALL** monitor for and take measures to prevent brute force attacks where the same or similar set of demographics are repeatedly searched with differing card information in an attempt to achieve a match when the card information is unknown.

The specification is:

* Only a SINGLE unique match **SHALL** be returned.
* No match **SHALL** return a 422 status code.
* Multiple matches **SHALL** return a 422 status code.

An important objective of this specification is to ensure that a health plan operating a $member-match operation has sufficient data provided to enable a match operation to be performed.  Therefore, the specification requires a health plan to provide demographic information (name, date of birth, gender) and identification details that would be present on a member's health plan insurance card with a request.

If a match is successful, the server **SHALL** return just the member identifier information for the patient.


#### Examples
An example request (as POSTed when invoking the operation) can be found [here](Parameters-member-match-in.html) and an example response (as received in the HTTP response body after the operation processes) can be found [here](Parameters-member-match-out.html).

