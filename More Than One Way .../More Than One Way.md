I'd like to use the phrase "more than one way to skin a cat", but some may not be familiar with that phrase, and some - my daughter being among them - would be offended by it.

A few months ago I had a SELECT of the form 
  `SELECT t1.a FROM t1 INNER JOIN t2 ON t1.link = t2.key WHERE t2.b = 'avalue';`
This statement was incredibly slow for some reason.  Surprisingly, what sped this up dramatically was changing the statement to
  `SELECT t1.a FROM t1 WHERE EXISTS (SELECT * FROM t2 WHERE t2.key = t1.link AND t2.b = 'avalue').`
(Of course, the original statement is kept in the comments since I STILL don't know why the second is faster, and I may need to revert to the original later).

For most SELECT statements, there are multiple ways of writing it, and sometimes the database you're using has a preference.  Last week, I found another major performance booster.  I had this DELETE statement:
  `DELETE FROM WICS_materiallist WHERE (id IN (SELECT DISTINCT delMaterialLink FROM WICS_tmpmateriallistupdate WHERE recStatus like "DEL%"))`.
This was my preferred way of writing this because the SELECT statement is used in a couple of places.  The SELECT usually doesn't return any records, but the 2-3 times per year it does, the corresponding records must be deleted from WICS_materiallist.  As WICS_materiallist grew, this particular statement got slower and slower.  Last week it was taking more than 4 minutes (240 seconds!!) to execute.  And the SELECT (legitimately) wasn't returning any records - there was nothing to delete!  At this point, I'm frustrated and my bosses are getting worse than frustrated.

The fix here was changing the DELETE to
    `DELETE MATL FROM WICS_materiallist AS MATL INNER JOIN WICS_tmpmateriallistupdate AS TMP ON MATL.id = TMP.delMaterialLink WHERE TMP.recStatus like "DEL%"`
(This form of DELETE is good for MySQL, check the documentation for your db).  The run time was nearly instantaneous.

There's more than one way to do things in SQL.  Sometimes it's worth it to try something you don't think should make a difference or think might make performance worse.  Sometimes your db's optimizer may surprise you.
