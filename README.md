-- For each of the following exercises, provide the appropriate query. Yes, even the ones that are expressed in the form of questions. Everything from class and the Sqlite Documentation is fair game.

-- 1. Provide a query showing Customers (just their full names, customer ID and country) who are not in the US.
SELECT FirstName, LastName, CustomerId, Country
FROM Customer
WHERE Country != "USA"

-- 2. Provide a query only showing the Customers from Brazil.
SELECT *
FROM Customer
WHERE Country == "Brazil"

-- 3. Provide a query showing the Invoices of customers who are from Brazil. The resultant table should show the customers full name, Invoice ID, Date of the invoice and billing country.
SELECT
FirstName || " " || LastName AS CustomerName, InvoiceDate, BillingCountry, InvoiceId
FROM Invoice iv
INNER JOIN Customer c ON c.CustomerId = iv.CustomerId
WHERE Country == "Brazil" ORDER BY CustomerName

-- 4. Provide a query showing only the Employees who are Sales Agents.
SELECT
*
FROM Employee
WHERE Title == "Sales Support Agent" 

-- 5. Provide a query showing a unique list of billing countries from the Invoice table.
SELECT DISTINCT
BillingCountry
FROM Invoice
ORDER BY BillingCountry 

-- 6. Provide a query that shows the invoices associated with each sales agent. The resultant table should include the Sales Agent's full name.
SELECT
FirstName || " " || LastName AS SalesAgentName, Title, InvoiceId
FROM Employee
INNER JOIN Invoice i
WHERE Title == "Sales Support Agent" ORDER BY InvoiceId

-- 7. Provide a query that shows the Invoice Total, Customer name, Country and Sale Agent name for all invoices and customers.
SELECT
c.FirstName || " " || c.LastName AS CustomerName,
c.Country,
e.FirstName || " " || e.LastName AS SalesAgentName,
i.Total
FROM Invoice i
INNER JOIN Employee e
INNER JOIN Customer c on c.SupportRepId = e.EmployeeId
WHERE e.Title == "Sales Support Agent"
ORDER BY InvoiceId

-- 8. How many Invoices were there in 2009 and 2011? What are the respective total sales for each of those years?(include both the answers and the queries used to find the answers)
SELECT
COUNT(InvoiceDate)
FROM Invoice
WHERE DATE(InvoiceDate) LIKE "2009%"
83

SELECT
COUNT(InvoiceDate)
FROM Invoice
WHERE DATE(InvoiceDate) LIKE "2011%"
83

SELECT
ROUND (SUM(Total),2)
FROM Invoice
WHERE DATE(InvoiceDate) LIKE "2009%"
$449.46

SELECT
ROUND (SUM(Total),2)
FROM Invoice
WHERE DATE(InvoiceDate) LIKE "2011%"
$469.58

-- 9. Looking at the InvoiceLine table, provide a query that COUNTs the number of line items for Invoice ID 37.
SELECT
COUNT(*)
FROM InvoiceLine
WHERE InvoiceId=37

-- 10. Looking at the InvoiceLine table, provide a query that COUNTs the number of line items for each Invoice. HINT: GROUP BY
SELECT
COUNT(InvoiceId), InvoiceId
FROM InvoiceLine
GROUP BY InvoiceId

--To Check:
SELECT
*
FROM InvoiceLine

-- 11. Provide a query that includes the track name with each invoice line item.
SELECT
i.TrackId, t.Name AS TrackName, InvoiceId
FROM InvoiceLine i
INNER JOIN Track t ON t.TrackId = i.TrackId
ORDER BY i.TrackId

-- 12. Provide a query that includes the purchased track name AND artist name with each invoice line item.
SELECT
i.TrackId, a.Name, t.Name AS TrackName
FROM InvoiceLine i
INNER JOIN Artist a
INNER JOIN Track t ON t.TrackId = i.TrackId
ORDER BY i.TrackId

-- 13. Provide a query that shows the # of invoices per country. HINT: GROUP BY
SELECT
COUNT(*), BillingCountry
FROM Invoice
GROUP BY BillingCountry

-- 14. Provide a query that shows the total number of tracks in each playlist. The Playlist name should be include on the resulant table.
SELECT
p.Name AS PlaylistName, p.PlaylistId, COUNT(p.PlaylistId) AS NumberOfTracks
FROM
Playlist p
INNER JOIN PlaylistTrack pt ON pt.PlaylistId = p.PlaylistId
GROUP BY p.PlaylistId

-- 15. Provide a query that shows all the Tracks, but displays no IDs. The resultant table should include the Album name, Media type and Genre.
SELECT
t.Name AS TrackName, a.Title AS AlbumName, m.Name AS MediaType, g.Name AS
GENRE
FROM Track t
INNER JOIN Album a ON a.AlbumId = t.AlbumId
INNER JOIN MediaType m ON m.MediaTypeId = t.MediaTypeId
INNER JOIN Genre g ON g.GenreId = t.GenreId
ORDER BY a.Title

-- 16. Provide a query that shows all Invoices but includes the # of invoice line items.
SELECT
COUNT(InvoiceLineId) AS NumberOfInvoiceLineItems, i.*
FROM Invoice i
INNER JOIN InvoiceLine il ON il.InvoiceId = i.InvoiceId
GROUP BY i.InvoiceId

-- 17. Provide a query that shows total sales made by each sales agent.
SELECT
ROUND(SUM(i.Total),2) AS TotalSales, 
e.FirstName || " " || e.LastName AS EmployeeName
FROM Invoice i
INNER JOIN EMPLOYEE e ON e.EmployeeId = c.SupportRepId
INNER JOIN CUSTOMER c ON c.CustomerId = i.CustomerId
WHERE e.Title == "Sales Support Agent"
GROUP BY EmployeeName

-- 18. Which sales agent made the most in sales in 2009? HINT: MAX
SELECT SalesAgentName, MAX(TotalSales)
FROM 
(
SELECT  e.FirstName|| ' ' || e.LastName AS SalesAgentName, ROUND(SUM(i.Total), 2) AS TotalSales 
FROM Employee AS e
INNER JOIN Customer AS c ON c.SupportRepId = e.EmployeeId
INNER JOIN Invoice AS i ON i.CustomerId = c.CustomerId
WHERE DATE(InvoiceDate) LIKE "2009%"
GROUP BY SalesAgentName
)

-- 19. Which sales agent made the most in sales over all?
SELECT SalesAgentName, MAX(TotalSales)
FROM 
(
SELECT  e.FirstName|| ' ' || e.LastName AS SalesAgentName, ROUND(SUM(i.Total), 2) AS TotalSales 
FROM Employee AS e
INNER JOIN Customer AS c ON c.SupportRepId = e.EmployeeId
INNER JOIN Invoice AS i ON i.CustomerId = c.CustomerId
GROUP BY SalesAgentName
)

-- 20. Provide a query that shows the # of customers assigned to each sales agent.
SELECT 
e.FirstName|| ' ' || e.LastName AS SalesAgentName, 
COUNT(c.CustomerId) AS NumberOfCustomers
FROM Employee e
INNER JOIN Customer c ON c.SupportRepId = e.EmployeeId
GROUP BY SalesAgentName

-- 21. Provide a query that shows the total sales per country. Which country's customers spent the most?
SELECT 
ROUND(SUM(Total), 2) AS TotalSales, BillingCountry
FROM Invoice
GROUP BY BillingCountry

SELECT Country, MAX(TotalSales)
FROM 
(
SELECT BillingCountry AS Country, ROUND(SUM(Total), 2) AS TotalSales
FROM Invoice
GROUP BY BillingCountry
)

-- 22. Provide a query that shows the most purchased track of 2013.
SELECT 
t.Name AS TrackName, 
COUNT(il.InvoiceLineId) AS NumberOfTimesPurchased
FROM InvoiceLine il
INNER JOIN Track t ON t.TrackId = il.TrackId
INNER JOIN Invoice i ON il.InvoiceId = i.InvoiceId
WHERE (i.InvoiceDate) LIKE "2013%"
GROUP BY t.Name
ORDER BY NumberOfTimesPurchased DESC

--23. Provide a query that shows the top 5 most purchased tracks over all.
SELECT 
t.Name AS TrackName, 
COUNT(il.InvoiceLineId) AS NumberOfTimesPurchased
FROM InvoiceLine il
INNER JOIN Track t ON t.TrackId = il.TrackId
INNER JOIN Invoice i ON il.InvoiceId = i.InvoiceId
GROUP BY t.Name
ORDER BY NumberOfTimesPurchased DESC
LIMIT 5

-- 24. Provide a query that shows the top 3 best selling artists.
SELECT 
a.Name AS ArtistName, 
COUNT(il.InvoiceLineId) AS NumberOfTimesPurchased
FROM InvoiceLine il
INNER JOIN Track t ON t.TrackId = il.TrackId
INNER JOIN Invoice i ON il.InvoiceId = i.InvoiceId
INNER JOIN Artist a ON a.ArtistId = al.ArtistId
INNER JOIN Album al ON al.AlbumId = t.AlbumId
GROUP BY a.Name
ORDER BY NumberOfTimesPurchased DESC
LIMIT 3

-- 25. Provide a query that shows the most purchased Media Type.
SELECT MediaType, MAX(NumberOfTimesPurchased)
FROM
(
SELECT
m.Name AS MediaType, 
COUNT(il.InvoiceLineId) AS NumberOfTimesPurchased
FROM InvoiceLine il
INNER JOIN MediaType m ON m.MediaTypeId = t.MediaTypeId
INNER JOIN Track t ON t.TrackId = il.TrackId
GROUP BY m.Name
)




